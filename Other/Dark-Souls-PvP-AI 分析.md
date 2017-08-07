---
title: Dark-Souls-PvP-AI 分析
tags: 
---

主循环函数
```cpp?linenums
void MainLogicLoop(){
		//TODO lock the camera
		//lockCamera(&processHandle);

		//read the data at these pointers, now that offsets have been added and we have a static address
        ReadPlayer(&Enemy, processHandle, EnemyId);
        ReadPlayer(&Player, processHandle, PlayerId);

        //log distance in memory
        AppendDistance(distance(&Player, &Enemy));

        //start the neural network threads
        WakeThread(defense_mind_input);
        WakeThread(attack_mind_input);

        ResetVJoyController();

		//generate instinct decision
		instinct_decision.subroutine_id.attackid = AtkNoneId;
		instinct_decision.subroutine_id.defenseid = DefNoneId;
		InstinctDecisionMaking(&instinct_decision);

        WaitForThread(defense_mind_input);
        guiPrint(LocationDetection",1:Defense Neural Network detected %d, and Attack %d", DefenseChoice, AttackChoice);
#if DebuggingPacifyDef
        DefenseChoice = 0;
#endif
		if (instinct_decision.priority_decision == EnterDodgeSubroutine || inActiveDodgeSubroutine() || (DefenseChoice>0)){
			dodge(&iReport, &instinct_decision, DefenseChoice);
		}

        WaitForThread(attack_mind_input);
        guiPrint(LocationDetection",2:Attack Neural Network decided %d", AttackChoice);
#if DebuggingPacifyAtk
        AttackChoice = 0;
#endif
		if (inActiveAttackSubroutine() || (AttackChoice && DefenseChoice <= 0)){
			attack(&iReport, &instinct_decision, AttackChoice);
        }

        //unset neural network desisions
        DefenseChoice = 0;
        AttackChoice = 0;
        //handle subroutine safe exits
        SafelyExitSubroutines();

        guiPrint(LocationDetection",5:Current Subroutine States ={%d,%d,%d,%d}", subroutine_states[0], subroutine_states[1], subroutine_states[2], subroutine_states[3]);

		//send this struct to the driver (only 1 call for setting all controls, much faster)
        guiPrint(LocationJoystick",0:AxisX:%d\nAxisY:%d\nButtons:0x%x", iReport.wAxisX, iReport.wAxisY, iReport.lButtons);
		UpdateVJD(iInterface, (PVOID)&iReport);

		//SetForegroundWindow(h);
		//SetFocus(h);
}
```

手动判断一些选择
```cpp?linenums
void InstinctDecisionMaking(InstinctDecision* instinct_decision){
	instinct_decision->priority_decision = EnemyNeutral;

	float distanceByLine = distance(&Player, &Enemy);
	guiPrint(LocationJoystick",1:Distance:%f", distanceByLine);
	unsigned char AtkID = isAttackAnimation(Enemy.animationType_id);

	//Actions are organized in reverse order of importance
	//Higher actions are less important
	//TODO should formalize this in an actual order somehow

	//if not two handing
	if (!Player.twoHanding && distanceByLine > 5){
		instinct_decision->priority_decision = EnterAttackSubroutine;
		instinct_decision->subroutine_id.attackid = TwoHandId;
	}
	//l hand bare handed, not holding shield. safety distance
	if (Player.l_weapon_id == 900000 && distanceByLine > 5){
		instinct_decision->priority_decision = EnterAttackSubroutine;
		instinct_decision->subroutine_id.attackid = SwitchWeaponId;
	}
	//heal if enemy heals
	if ((Enemy.animationType_id == CrushUseItem || Enemy.animationType_id == EstusSwig_part1 || Enemy.animationType_id == EstusSwig_part2) && Player.hp < 2000){
		instinct_decision->priority_decision = EnterAttackSubroutine;
		instinct_decision->subroutine_id.attackid = HealId;
	}

	//if enemy in range and we/enemy is not in invulnerable position (bs knockdown)
	if (distanceByLine <= Enemy.weaponRange && !Player.in_backstab && !Enemy.in_backstab){
		if (
			//if in an animation where subanimation is not used for hurtbox
			(AtkID == 3 && Enemy.subanimation <= AttackSubanimationActiveDuringHurtbox) ||
			//or animation where it is
			((AtkID == 2 || AtkID == 4) && (Enemy.subanimation >= AttackSubanimationWindupClosing && Enemy.subanimation <= AttackSubanimationActiveDuringHurtbox))
			)
		{
			OverrideLowPrioritySubroutines();
			guiPrint(LocationDetection",0:about to be hit (anim type id:%d) (suban id:%d)", Enemy.animationType_id, Enemy.subanimation);
			instinct_decision->priority_decision = EnterDodgeSubroutine;

			//Decide on dodge action

			//if we got hit already, and are in a state we can't dodge from, toggle escape the next hit
			if (Player.subanimation == PoiseBrokenSubanimation && (Enemy.dodgeTimeRemaining > 0.2 && Enemy.dodgeTimeRemaining < 0.3))
			{
				instinct_decision->subroutine_id.defenseid = ToggleEscapeId;
			}
			//while staggered, dont enter any subroutines
			if (Player.subanimation != PoiseBrokenSubanimation)
			{
				if (
					//if the reverse roll is close enough to put us behind the enemy and we have enough windup time to reverse roll
					distance(&Player, &Enemy) <= 3 && TotalTimeInSectoReverseRoll < Enemy.dodgeTimeRemaining &&
					//if just reverse rolled and next incoming attack and weapon speed < ?, do normal roll
					(last_subroutine_states_self[0] != ReverseRollBSId || TotalTimeInSectoReverseRoll + 0.3 > Enemy.dodgeTimeRemaining)
					)
				{
					instinct_decision->subroutine_id.defenseid = ReverseRollBSId;
				}
				else if (
					//if we dont have enough time to roll
					Enemy.dodgeTimeRemaining < 0.15 && Enemy.dodgeTimeRemaining > 0 && 
					//we have a shield equipped/are two handing
					(Player.twoHanding || IsWeaponShield(Player.l_weapon_id)) &&
					//we're in a neutral state
					Player.subanimation == SubanimationNeutral)
				{
					instinct_decision->subroutine_id.defenseid = PerfectBlockId;
				}
				//otherwise, normal roll
				else{
					instinct_decision->subroutine_id.defenseid = StandardRollId;
				}
			}
			//if we had to toggle escape, they're probably comboing. Get out.
			if (last_subroutine_states_self[0] == ToggleEscapeId){
				instinct_decision->subroutine_id.defenseid = StandardRollId;
			}
		}
		//windup, attack coming
		else if (AtkID == 1 || ((AtkID == 2 || AtkID == 4) && Enemy.subanimation == AttackSubanimationWindup)){
			guiPrint(LocationDetection",0:dont attack, enemy windup");
			instinct_decision->priority_decision = DelayActions;
		}
	}

	//backstab checks. If AI can BS, always take it
	unsigned char BackStabStateDetected = BackstabDetection(&Player, &Enemy, distanceByLine);
	if (BackStabStateDetected){
		OverrideLowPrioritySubroutines();

		guiPrint(LocationDetection",0:backstab state %d", BackStabStateDetected);
		//in position to bs
		if (BackStabStateDetected == 2){
			instinct_decision->priority_decision = EnterAttackSubroutine;
			instinct_decision->subroutine_id.attackid = GhostHitId;
		}
		//try and move up for bs
		else if (BackStabStateDetected == 1){
			instinct_decision->priority_decision = EnterAttackSubroutine;
			instinct_decision->subroutine_id.attackid = MoveUpId;
		}
	}

	if (instinct_decision->priority_decision == EnemyNeutral){
		guiPrint(LocationDetection",0:not about to be hit (enemy animation type id:%d) (enemy subanimation id:%d)", Enemy.animationType_id, Enemy.subanimation);
	}
}
```

训练神经网络的网络定义
```cpp?linenums
//use the file to train the network
void trainFromFile(unsigned int max_neurons, const char* training_file, const char* testing_file, const char* output_file){
    struct fann *ann;
    struct fann_train_data *train_data, *test_data;
    const float desired_error = (const float)0.05;
    unsigned int neurons_between_reports = 5;

    printf("Reading data.\n");

    train_data = fann_read_train_from_file(training_file);
    test_data = fann_read_train_from_file(testing_file);

    fann_scale_train_data(train_data, -1, 1);

    printf("Creating network.\n");
    printf("input number:%d, output number:%d\n", fann_num_input_train_data(train_data), fann_num_output_train_data(train_data));

    ann = fann_create_shortcut(2, fann_num_input_train_data(train_data), fann_num_output_train_data(train_data));

    fann_set_training_algorithm(ann, FANN_TRAIN_RPROP);
    fann_set_activation_function_hidden(ann, FANN_SIGMOID_SYMMETRIC);
    fann_set_activation_function_output(ann, FANN_LINEAR);
    fann_set_train_error_function(ann, FANN_ERRORFUNC_LINEAR);
    fann_set_bit_fail_limit(ann, (fann_type)0.9);
    fann_set_train_stop_function(ann, FANN_STOPFUNC_BIT);
    fann_print_parameters(ann);

    printf("Training network.\n");
    fann_cascadetrain_on_data(ann, train_data, max_neurons, neurons_between_reports, desired_error);
    fann_print_connections(ann);

    float mse_train = fann_test_data(ann, train_data);
    unsigned int bit_fail_train = fann_get_bit_fail(ann);
    float mse_test = fann_test_data(ann, test_data);
    unsigned int bit_fail_test = fann_get_bit_fail(ann);

    printf("\nTrain error: %f, Train bit-fail: %d, Test error: %f, Test bit-fail: %d\n\n",
        mse_train, bit_fail_train, mse_test, bit_fail_test);

    for (unsigned int i = 0; i < train_data->num_data; i++)
    {
        fann_type* output = fann_run(ann, train_data->input[i]);
        if (abs(train_data->output[i][0] - output[0]) > 5 || train_data->output[i][0] >= 0 && output[0] <= 0 || train_data->output[i][0] <= 0 && output[0] >= 0)
        {
            printf("ERROR: %f does not match %f\n", train_data->output[i][0], output[0]);
        }
    }


    printf("Saving network.\n");
    fann_save(ann, output_file);

    printf("Cleaning up.\n");
    fann_destroy_train(train_data);
    fann_destroy(ann);
}
```

防御进程, 调用fann训练的防御网络
```cpp?linenums
DWORD WINAPI DefenseMindProcess(void* data){
    while (!defense_mind_input->exit)
    {
        //lock control of this resource
        EnterCriticalSection(&(defense_mind_input->crit));
        //wait for the indicator this should run, and release lock in meantime
        while (defense_mind_input->runNetwork == false){
            SleepConditionVariableCS(&(defense_mind_input->cond), &(defense_mind_input->crit), INFINITE);
        }

        //generate inputs and scale from -1 to 1 
        fann_type input[8];

        //copy inputs into input and scale
        float mostRecentDistance = distance(&Player, &Enemy);
        input[0] = SCALE(mostRecentDistance, 0, 10);
        input[0] = input[0] > 1 ? 1 : input[0];
        input[0] = input[0] < -1 ? -1 : input[0];
        for (int i = 0; i < 4; i++){
            input[i+1] = SCALE(DistanceMemory[i], 0, 10);
            //cut off above and below
            input[i+1] = input[i+1] > 1 ? 1 : input[i+1];
            input[i+1] = input[i+1] < -1 ? -1 : input[i+1];
        }
        input[5] = SCALE(angleDeltaFromFront(&Player, &Enemy), 0, 1.6);
        input[6] = SCALE(Enemy.velocity, -0.18, -0.04);
        input[7] = SCALE(rotationDifferenceFromSelf(&Player, &Enemy), 0, 3.8);

        fann_type* out = fann_run(defense_mind_input->mind, input);
		//printf("%f\n", *out);

		//backstab attempt detection and avoidace
		//TODO implement more types of backstab avoidance actions
        if (*out < 10 && *out > 0.5
            && mostRecentDistance < 5 //hardcode bs distance
            && Enemy.subanimation == SubanimationNeutral //enemy cant backstab when in animation
			//&& BackstabDetection(&Enemy, &Player, mostRecentDistance) == 0 //can't be backstabed when behind enemy
        ){
			//TODO make this strafe in the same direction as the enemy strafe
            DefenseChoice = CounterStrafeLeftId;
        } 

		//if we're waking up from a bs, try to avoid chain
		if (Player.in_backstab){
			if (rand() > RAND_MAX / 2){
				//randomly choose between chain escapes to through off predictions
				DefenseChoice = OmnistepBackwardsId;
			}
			else{
				DefenseChoice = ReverseRollBSId;
			}

		}

		//if the enemy is close behind us, and there's no possibilty of chain(which a bs cancel can't prevent) try to damage cancel their bs.
		if (BackstabDetection(&Enemy, &Player, mostRecentDistance) && !Player.in_backstab && !Enemy.in_backstab){
			AttackChoice = GhostHitId;
		}

        //prevent rerun
        defense_mind_input->runNetwork = false;
        //release lock
        LeaveCriticalSection(&(defense_mind_input->crit));
        WakeConditionVariable(&(defense_mind_input->cond));
    }
    return 0;
}
```

Defense Network Inputs:  

  * Array of distance between AI and enemy over 0.5 second period 
  * The angle the enemy is off from directly in front of the player  
  * The enemy velocity  
  * The rotation difference between the enemy and the player  


攻击进程, 调用fann训练的攻击网络
```cpp?linenums
DWORD WINAPI AttackMindProcess(void* data){
    while (!attack_mind_input->exit)
    {
        //lock control of this resource
        EnterCriticalSection(&(attack_mind_input->crit));
        //wait for the indicator this should run, and release lock in meantime
        while (attack_mind_input->runNetwork == false){
            SleepConditionVariableCS(&(attack_mind_input->cond), &(attack_mind_input->crit), INFINITE);
        }

        //generate inputs and scale from -1 to 1 
        fann_type input[DistanceMemoryLENGTH + 5 + AIHPMemoryLENGTH + 1 + last_animation_types_enemy_LENGTH + 1];

        //copy inputs into input and scale
        float mostRecentDistance = distance(&Player, &Enemy);
        input[0] = SCALE(mostRecentDistance, 0, 10);
        input[0] = input[0] > 1 ? 1 : input[0];
        input[0] = input[0] < -1 ? -1 : input[0];
        for (int i = 0; i < DistanceMemoryLENGTH-1; i++){
            input[i+1] = SCALE(DistanceMemory[i], 0, 10);
            //cut off above and below
            input[i+1] = input[i+1] > 1 ? 1 : input[i+1];
            input[i+1] = input[i+1] < -1 ? -1 : input[i+1];
        }
        input[DistanceMemoryLENGTH] = SCALE(StaminaEstimationEnemy(), -40, 192);
        input[DistanceMemoryLENGTH + 1] = SCALE(Enemy.poise, 0, 120);
        input[DistanceMemoryLENGTH + 2] = SCALE(PoiseDamageForAttack(Player.r_weapon_id, 46), 0, 80);
        input[DistanceMemoryLENGTH + 3] = SCALE(Player.poise, 0, 120);
        input[DistanceMemoryLENGTH + 4] = SCALE(PoiseDamageForAttack(Enemy.r_weapon_id, 46), 0, 80);
        for (int i = 0; i < AIHPMemoryLENGTH; i++){
            input[i + DistanceMemoryLENGTH + 5] = SCALE(AIHPMemory[i], 0, 2000);
        }
        input[DistanceMemoryLENGTH + 5 + AIHPMemoryLENGTH] = SCALE(Player.stamina, -40, 192);
        for (int i = 0; i < last_animation_types_enemy_LENGTH; i++){
            input[i + DistanceMemoryLENGTH + 5 + AIHPMemoryLENGTH + 1] = SCALE(last_animation_types_enemy[i], 0, 255);
        }
        input[DistanceMemoryLENGTH + 5 + AIHPMemoryLENGTH + 1 + last_animation_types_enemy_LENGTH] = SCALE(Player.bleedStatus, 0, 255);

        fann_type* out = fann_run(attack_mind_input->mind, input);

		//potentally move up if not in range
		if (mostRecentDistance > Player.weaponRange){
			AttackChoice = MoveUpId;
		}

		//TODO desicion about going for a backstab. Note that these subroutines will attempt, not garuntee
		//AttackChoice = PivotBSId;

		//TODO chain bs's. if enemy in bs, try chain

		//Decision about standard attack
        if (
			!BackstabMetaOnly &&
            //sanity checks
            mostRecentDistance <= Player.weaponRange && //in range
            Player.stamina > 20 && //just to ensure we have enough to roll
            Player.bleedStatus > 40 && //more than one attack to proc bleed
            //static checks for attack
            ((
                (Player.stamina > 90) && //safety buffer for stamina
                (Enemy.subanimation >= LockInSubanimation && Enemy.subanimation < SubanimationNeutral)  //enemy in vulnerable state, and can't immediatly transition
            ) ||
            (*out > 0.5)//neural network says so
           ))
        {
            //randomly choose dead angle or ghost hit
            //throw off enemy predictions
            if (rand() > RAND_MAX / 2){
                AttackChoice = DeadAngleId;
            }
            else{
                AttackChoice = GhostHitId;
            }
        }

        //prevent rerun
        attack_mind_input->runNetwork = false;
        //release lock
        LeaveCriticalSection(&(attack_mind_input->crit));
        WakeConditionVariable(&(attack_mind_input->cond));
    }
    return 0;
}
```

Attack Network Inputs:  

  * Array of distance between AI and enemy over 5 second period
  * Estimated stamina of enemy
  * Poise of enemy 
  * poise damage of AI's attack
  * the AI's current poise
  * base poise damage of enemy's attack
  * array of AI's HP over time
  * stamina of AI
  * array of enemy animation types
  * current bleed build up
  * NOTES:
    * usually tell how they attack by how long it's been since they last attacked