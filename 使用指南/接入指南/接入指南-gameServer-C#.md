## 准备

在开发之前，需要在Matchvs官网创建gameServer并下载框架，详情可参考[gameServer接入流程](/service?page=gameServer接入流程)。

## 开始

直接双击打开gameServer_csharp.sln

## 创建房间/加入房间

当客户端调用创建或者加入房间功能时，gameServer这边会收到2个消息，create和join消息，开发者只需要在`createRoom`和`joinRoom`方法下实现客户端加入房间后，游戏服务端需要处理的逻辑。

```c#
    /// <summary>
    /// 创建房间
    /// </summary>
    /// <param name="msg"></param>
    public override IMessage OnCreateRoom(ByteString msg)
    {
        Request request = new Request();
        ByteUtils.ByteStringToObject(request, msg);

        Reply reply = new Reply()
        {
            UserID = request.UserID,
            GameID = request.GameID,
            RoomID = request.RoomID,
            Errno = ErrorCode.Ok,
            ErrMsg = "OnCreateRoom success"
        };

        Logger.Info("OnCreateRoom start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

        return reply;
    }
    /// <summary>
    /// 加入房间
    /// </summary>
    /// <param name="msg"></param>
    public override IMessage OnJoinRoom(ByteString msg)
    {
        Request request = new Request();
        ByteUtils.ByteStringToObject(request, msg);

        Reply reply = new Reply()
        {
            UserID = request.UserID,
            GameID = request.GameID,
            RoomID = request.RoomID,
            Errno = ErrorCode.Ok,
            ErrMsg = "OnJoinRoom success"
        };

        Logger.Info("OnJoinRoom start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

        return reply;
    }
```



## 停止加入

当客户端调用停止加入接口（`joinOver()`）时，gameServer会触发`onJoinOver()`，开发者可以将收到客户端停止加入后的逻辑写到该方法里。

```c#
    /// <summary>
    /// 加入房间Over
    /// </summary>
    /// <param name="msg"></param>
    public override IMessage OnJoinOver(ByteString msg)
    {
        Request request = new Request();
        ByteUtils.ByteStringToObject(request, msg);

        Reply reply = new Reply()
        {
            UserID = request.UserID,
            GameID = request.GameID,
            RoomID = request.RoomID,
            Errno = ErrorCode.Ok,
            ErrMsg = "OnJoinOver success"
        };

        Logger.Info("OnJoinOver start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

        return reply;
    }
```



Matchvs提供了在gameServer里主动发起JoinOver的接口。调用该接口向Matchvs通知不要再向房间加人。

```c#
    /// <summary>
    /// 主动推送给MVS，房间不可以再加人
    /// </summary>
    public void PushJoinOver(UInt64 roomId, UInt32 gameId, UInt32 userId = 0, UInt32 version = 2)
    {
        Logger.Info("PushJoinOver, roomID:{0}, gameID:{1}", roomId, gameId);

        JoinOverReq joinReq = new JoinOverReq()
        {
            RoomID = roomId,
            GameID = gameId
        };
        baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsJoinOverReq, joinReq);
    }
```



## 发送数据

调用`push()`接口，可以在gameServer里给各个客户端异步发送数据。支持发给指定客户端。

```c#
    /// <summary>
    /// 推送给Hotel，根据roomID来区分是哪个Hotel
    /// </summary>
    /// <param name="roomID"></param>
    /// <param name="msg"></param>
    public void PushToHotel(UInt64 roomID, IMessage msg, UInt32 userId = 1, UInt32 version = 2)
    {
        baseServer.PushToHotel(userId, version, roomID, (UInt32)HotelGsCmdID.HotelPushCmdid, msg);
    }
```



## 离开房间

当客户端调用离开房间时，gameServer会触发`onLeaveRoom()`，开发者可以将收到客户端离开房间时的相关逻辑写到该方法里。

```c#
    /// <summary>
    /// 离开房间
    /// </summary>
    /// <param name="msg"></param>
    public override IMessage OnLeaveRoom(ByteString msg)
    {
        Request request = new Request();
        ByteUtils.ByteStringToObject(request, msg);

        Reply reply = new Reply()
        {
            UserID = request.UserID,
            GameID = request.GameID,
            RoomID = request.RoomID,
            Errno = ErrorCode.Ok,
            ErrMsg = "OnLeaveRoom success"
        };

        Logger.Info("OnLeaveRoom start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

        return reply;
    }
```



## 踢除房间成员

当客户端调用踢人时，gameServer会触发`onKickPlayer()`，开发者可以将"收到客户端踢人时的相关逻辑"写到该方法里。

```c#
	/// <summary>
    /// 踢人
    /// </summary>
    /// <param name="msg"></param>
    public override IMessage OnKickPlayer(ByteString msg)
    {
        Request request = new Request();
        ByteUtils.ByteStringToObject(request, msg);

        Reply reply = new Reply()
        {
            UserID = request.UserID,
            GameID = request.GameID,
            RoomID = request.RoomID,
            Errno = ErrorCode.Ok,
            ErrMsg = "OnKickPlayer success"
        };

        Logger.Info("OnKickPlayer start, userId={0}, gameId={1}, roomId={2}", request.UserID, request.GameID, request.RoomID);

        return reply;
    }
```

Matchvs提供了在gameServer里踢除房间成员的接口。当发现有玩家恶意不准备等情况，可以调用该接口将该玩家踢出房间。

```c#
    /// <summary>
    /// 主动推送给MVS，踢掉某人
    /// </summary>
    /// <param name="roomId"></param>
    /// <param name="srcId"></param>
    /// <param name="destId"></param>
    public void PushKickPlayer(UInt64 roomId, UInt32 destId, UInt32 userId = 0, UInt32 version = 2)
    {
        Logger.Info("PushKickPlayer, roomID:{0}, srcId:{1}, destId:{2}", roomId, srcId, destId);

        KickPlayer kick = new KickPlayer()
        {
            RoomID = roomId,
            UserID = destId
        };
        baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsKickPlayerReq, kick);
    }
```



## 连接状态

当客户端和服务端的连接出现断开、重连等情况时，gameServer会触发连接状态的通知。

```c#
    /// <summary>
    /// 连接状态
    /// </summary>
    /// <param name="msg"></param>
    public override IMessage OnConnectStatus(ByteString msg)
    {
        Request request = new Request();
        ByteUtils.ByteStringToObject(request, msg);

        Reply reply = new Reply()
        {
            UserID = request.UserID,
            GameID = request.GameID,
            RoomID = request.RoomID,
            Errno = ErrorCode.Ok,
            ErrMsg = "OnConnectStatus success"
        };
        string status = request.CpProto.ToStringUtf8();

        Logger.Info("OnConnectStatus start, userId={0}, gameId={1}, roomId={2}, status = {3}", request.UserID, request.GameID, request.RoomID, status);

        //1.掉线了  2.重连成功  3.重连失败
        if (status == "3")
        {
            Logger.Info("OnConnectStatus leaveroom, userId={0}, gameId={1}, roomId={2}, status = {3}", request.UserID, request.GameID, request.RoomID, status);
            return OnLeaveRoom(msg);
        }

        Logger.Info("OnConnectStatus end, userId={0}, gameId={1}, roomId={2}, status = {3}", request.UserID, request.GameID, request.RoomID, status);

        return reply;
    }
```

## 房间详情

Matchvs提供了在gameServer里查询房间详情的接口，查询结果在`onRoomDetail`中返回。

```c#
	/// <summary>
    /// 获取房间详情
    /// </summary>
    /// <param name="roomId"></param>
    /// <param name="gameId"></param>
    public void PushGetRoomDetail(UInt64 roomId, UInt32 gameId, UInt32 userId = 0, UInt32 version = 2)
    {
        Logger.Info("PushGetRoomDetail, roomID:{0}, gameId:{1}", roomId, gameId);
        GetRoomDetailReq roomDetail = new GetRoomDetailReq()
        {
            RoomID = roomId,
            GameID = gameId
        };
        baseServer.PushToMvs(userId, version, (UInt32)MvsGsCmdID.MvsGetRoomDetailReq, roomDetail);
    }

	/// <summary>
    /// 房间详情
    /// </summary>
    /// <param name="msg"></param>
    public override void OnRoomDetail(ByteString msg)
    {
        Request request = new Request();
        ByteUtils.ByteStringToObject(request, msg);

        RoomDetail roomDetail = new RoomDetail();
        ByteUtils.ByteStringToObject(roomDetail, request.CpProto);

        Logger.Info("OnRoomDetail, roomId={0}, state={1}, maxPlayer={2}, mode={3}, canWatch={4}, owner={5}",
            roomDetail.RoomID, roomDetail.State, roomDetail.MaxPlayer, roomDetail.Mode, roomDetail.CanWatch, roomDetail.Owner);
        foreach (PlayerInfo player in roomDetail.PlayerInfos)
        {
            Logger.Info("player userId={0}", player.UserID);
        }
    }
```

