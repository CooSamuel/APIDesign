# API Design Repo with Swagger

## VScode with [vs-swagger-viewer](https://github.com/arjun-g/vs-swagger-viewer.git)

![swagger-viewer](https://ws3.sinaimg.cn/large/006tKfTcgy1g1gg0a6iumj30fm0m1aad.jpg)

## API List

### Message

- **GET** /message

  > Get (paginated) messages, used in homepage & featured

  - Reqest:
    - `query`:
      - `startId`: messageId
      - `count`: number of messages returned
      - `tag`: tag of message ('all' to select all)
  - Response:
    - Success
      - status: 200
      - data:
        ```javascript
        {
          messages: [
            {
              _id: ObjectId,
              user: {
                _id: ObjectId,
                userInfo: {
                  nickname: String,
                  gender: String,
                  avatarUrl: String
                }
              },
              content: String,
              imgUrl: [String],
              tag: String,
              location: String,
              anony: String,
              likes: [Like], // contains userId
              comments: [Comment], // array of ids
              created_at: String
            }
          ];
        }
        ```
    - Failure
      - status: `400 / 404`
      - data:
        ```javascript
        {
            err: ErrorObject,
            message: String,
        }
        ```

- **GET** /message/:msgId

  > Get one message by \_id, used in message detail page

  - Reqest:
    - `params`:
      - `msgId`: \_id of message
  - Response:
    - Success
      - status: 200
      - data:
        ```javascript
        {
            message: {
                _id: ObjectId,
                user: {
                    _id: ObjectId,
                    userInfo: {
                        nickname: String,
                        gender: String,
                        avatarUrl: String,
                    }
                },
                content: String,
                imgUrl: [String],
                tag: String,
                location: String,
                anony: String,
                likes: [Like], // contains userId
                comments: [Comment], // array of ids
                created_at: String,
            }
        }
        ```

- **POST** /message

  > Add a new message (may contain images)

  - Reqest:
    - `body`:
      ```javascript
        {
            message: {
                user: ObjectId,
                content: String,
                imgUrl: [String],
                tag: String,
                location: String,
                anony: String,
            }
        }
      ```

- **DELETE** /message/:msgId
  > Delete an message by \_id (**Should delete all comments under it!**)
  - Reqest:
    - `params`:
      - `msgId`: \_id of message

### Comment

- **GET** /comment/:msgId

  > Get all comments under a message

  - Reqest:
    - `params`:
      - `msgId`: \_id of message
  - Response:
    - Success
      - status: 200
      - data:
        ```javascript
        {
          comments: [
            {
              _id: ObjectId,
              user: {
                _id: ObjectId,
                userInfo: {
                  nickname: String,
                  gender: String,
                  avatarUrl: String
                }
              },
              content: String,
              annoy: String,
              likeNum: Number,
              targetCom: ObjectId, // Optional
              created_at: String
            }
          ];
        }
        ```

- **POST** /comment/:msgId

  > Add a new comment under a message

  - Reqest:
    - `params`:
      - `msgId`: \_id of message
    - `body`:
      ```javascript
        {
            comment: {
                user: ObjectId,
                content: String,
                annoy: String,
                targetCom: ObjectId, // Optional
            }
        }
      ```

- **DELETE** /comment/:comId
  > Delete an comment by \_id
  - Reqest:
    - `params`:
      - `comId`: \_id of comment

### User

- **GET** /user/:userId

  > Get a user by \_id, used in profile page

  - Reqest:
    - `params`:
      - `userId`: \_id of user
  - Response:
    - Success
      - status: 200
      - data:
        ```javascript
        {
            user: {
                _id: ObjectId,
                openId: String,
                unionId: String,
                userInfo: {
                    nickname: String,
                    gender: String,
                    avatarUrl: String,
                }
            }
        }
        ```

- **POST** /user

  > Login or create a user (may update WeChat userInfo)

  - Reqest:
    - `body`:
      ```javascript
        {
            code: String, // wx.login() => code
            iv: String,
            encryptedData: String,
            signature: String,
        }
      ```
  - Response:
    - Success:
      - code: 200
      - data:
        ```javascript
        {
            user: User,
            token: String,
        }
        ```

- **PUT** /user/:userId
  > Update the user's WeChat info
  - Request:
    - `params`:
      - `userId`: \_id of user
    - `body`:
      ```javascript
      {
          userInfo: {
              nickname: String,
              gender: String,
              city: String,
              language: String,
              province: String,
              country: String,
              avatarUrl: String,
          }
      }
      ```

### Like

- **POST** /like/:type/:relatedId

  > Add a new like under given type (message/comment)

  - Reqest:
    - `params`:
      - `type`: message or comment
      - `relatedId`: \_id of instance
    - `body`:
      ```javascript
      {
        user: ObjectId,
        targetUser: ObjectId
      }
      ```

- **DELETE** /like/:type/:relatedId
  > Remove a like under given type (message/comment)
  - Reqest:
    - `params`:
      - `type`: message or comment
      - `relatedId`: \_id of instance
    - `body`:
      ```javascript
      {
        user: ObjectId,
        targetUser: ObjectId
      }
      ```

### Notification

- **GET** /notification/:userId

  > Get a user by \_id, used in profile page

  - Reqest:
    - `params`:
      - `userId`: \_id of user
  - Response:
    - Success
      - status: 200
      - data:
        ```javascript
        {
          notifications: [{
              fromUser: {
                  id: ObjectId,
                  userInfo: {
                      nickname: String,
                      gender: String,
                      avatarUrl: String,
                  }
              },
              type: String,
              relatedMsg: {
                  _id: ObjectId,
                  user: {
                      id: ObjectId,
                      userInfo: {
                          nickname: String,
                          gender: String,
                          avatarUrl: String,
                      }
                  }
                  content: String,
                  annoy: String,
                  created_at: String,
              }
              relatedCom: {
                  _id: ObjectId,
                  user: {
                      id: ObjectId,
                      userInfo: {
                          nickname: String,
                          gender: String,
                          avatarUrl: String,
                      }
                  }
                  content: String,
                  annoy: String,
                  created_at: String,
              }
              isRead: Boolean,
              created_at: String
            }]
        }
        ```

- **PUT** /notification/:notiId
  > Update the notification isRead status
  - Request:
    - `params`:
      - `notiId`: \_id of notification

### Chatroom

- **GET** /chatroom/:userId

  > Get a user by \_id, used in profile page

  - Reqest:
    - `params`:
      - `userId`: \_id of user
  - Response:

    - Success

      - status: 200
      - data:

        ```javascript
        {
          chatroom: [
            {
              users: [
                {
                  _id: ObjectId,
                  userInfo: {
                    nickname: String,
                    gender: String,
                    avatarUrl: String
                  }
                }
              ],
              lastChat: {
                content: String,
                imgUrl: String,
                created_at: String
              },
              created_at: String
            }
          ];
        }
        ```
