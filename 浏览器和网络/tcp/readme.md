# 三次握手
1. 客户端向服务器发出syn招手， 此时客户端变成半打开状态， 服务器端也变成半打开状态
2. 服务器端向客户端发出syn招手， 再向客户端发出点头ack， 此时客户端接收到ack变成完成状态， 
3. 客户端向服务器端发出点头ack， 服务器端接收到后变成完成状态
# 四次挥手
1. 客户端向服务器端发出fn挥手， 此时变成等待状态
2. 服务器端接受到fn后， 变成close_wait状态， 并且向客户端发出ack点头
3. 客户端接受到ack点头后， 变成第二个等待状态
4. 服务器端向客户端发出fn挥手， 此时变成last_ack
5. 客户端向服务器端发出ack点头后， 客户端变成time_wait状态
6. 服务器端接受到ack点头后， 变成close状态， 此时过四分钟后， 客户端变成close状态