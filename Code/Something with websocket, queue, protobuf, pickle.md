---
title: Something with websocket, queue, protobuf, pickle
---

Last day I encountered with two bug.
First one is related with muitiprocessing and queue.
I orignally used queue in my websocket communication part.

```
import datetime
import queue
from ws4py.client.threadedclient import WebSocketClient
from krfapis.util.util import init_logger

__all__ = ['WSClient']


class WSClient(WebSocketClient):
    def init_logger(self, log_path=None):
        self.logger = init_logger('ws_{}'.format(datetime.datetime.now().strftime('%Y%m%d_%H%M%S')), log_path)
        self.msg_q = queue.Queue()

    def send(self, payload, binary=False):
        self.logger.debug("Send message {}".format(payload))
        super(WSClient, self).send(payload, binary)

    def opened(self):
        self.logger.debug("Connection opened.")

    def closed(self, code, reason=None):
        self.logger.debug("Closed down, code: {}, reason: {}.".format(code, reason))

    def get_message(self):
        return self.msg_q.get()

    def received_message(self, m):
        self.logger.debug("Receive message {}".format(m.data))
        self.msg_q.put(m.data)

```

It worked well for like a month. But when I implement several parallel connections using multiprocessing, weird things happened, the queue couldn't get the reply and so the program stuck in there. First I thought there was something wrong with connections, but later I found that there was a different implement of queue in multiprocessing. So I changed the code and it works.

```
import datetime
from multiprocessing import Queue
from ws4py.client.threadedclient import WebSocketClient
from krfapis.util.util import init_logger

__all__ = ['WSClient']


class WSClient(WebSocketClient):
    def init_logger(self, log_path=None):
        self.logger = init_logger('ws_{}'.format(datetime.datetime.now().strftime('%Y%m%d_%H%M%S')), log_path)
        self.msg_q = Queue()

    def send(self, payload, binary=False):
        self.logger.debug("Send message {}".format(payload))
        super(WSClient, self).send(payload, binary)

    def opened(self):
        self.logger.debug("Connection opened.")

    def closed(self, code, reason=None):
        self.logger.debug("Closed down, code: {}, reason: {}.".format(code, reason))

    def get_message(self):
        return self.msg_q.get()

    def received_message(self, m):
        self.logger.debug("Receive message {}".format(m.data))
        self.msg_q.put(m.data)
		
```
So you can't use the simple version of queue in a multiprocessing environment. I think the reason is it is not process safe, so it can't deal any data coming from another process.

Another bug is about pickle and protobuf.
You can not push a protobuf object to a multiprocessing queue, because the object can not be pickled.