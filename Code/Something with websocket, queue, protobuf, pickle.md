---
title: Something with websocket, queue, protobuf, pickle
---

Last day I encountered with two bug.
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

It worked well for like a month. But when I implement several parallel connections using multiprocessing, weird things happened, the queue couldn't get the reply and so the program stuck in there.