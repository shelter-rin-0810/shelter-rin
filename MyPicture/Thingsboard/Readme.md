# Thingsborad 

## 网关设备说明

- **连接**
  - |---- 地址：8.140.203.57:1883
  - |---- token：ol9bifoyelzjx2cdwffl
  ---
- **话题及其功能**
  - |--- 概览
    Topic | 功能
    ------------ | -------------
    v1/gateway/connect    | 创建新设备，表示设备已经连接到tb平台
    v1/gateway/disconnect | 断开设备
    v1/gateway/attributes | 将**设备属性**更新发布到tb平台/从tb平台订阅共享属性更新
    v1/gateway/attributes/request |向tb平台请求客户端或共享设备属性，在此之前需要订阅v1/gateway/attributes/response
    v1/gateway/telemetry | 将**遥测数据**发布到tb平台
    v1/gateway/rpc | 订阅rpc命令
    v1/gateway/claim | 声明设备id
  - |---- 详细格式
    -   v1/gateway/connect:
        > 向该话题发布如下消息以创建新设备.device中跟的是设备的名称，创建后tb平台显示如图，不过测试过一次好像只能创建一个设备，"device":["Device A","Device B"]这样是不行的
        >![创建设备示意图](https://github.com/shelter-rin-0810/shelter-rin/blob/main/MyPicture/Thingsboard/gateway_connect.jpg?raw=true "创建设备示意图")
        ```javascript {.line-numbers}
        {
          "device":"Device A"
        }
        ```
    -   v1/gateway/disconnect:
        > 断开设备的连接，发布后ThingsBoard将不再向此网关发布该特定设备的更新
        ```javascript {.line-numbers}
        {
          "device":"Device A"
        }
        ```
    -   v1/gateway/attributes:
        
        > 更新**设备属性**，将以下**PUBLISH**消息发送到主题，更新的是设备属性，在tb平台这处可以查看
        >![设备属性](https://github.com/shelter-rin-0810/shelter-rin/blob/main/MyPicture/Thingsboard/device_attributes.jpg?raw=true "设备属性")
        ```javascript {.line-numbers}
        { 
          "Device A":{"attribute1":"value1", "attribute2": 42}, 
          "Device B":{"attribute1":"value1", "attribute2": 42}
        }
        ```
        > **订阅**共享属性更新，将**SUBSCRIBE**消息发送到主题，表示期望消息的结果
        ```javascript {.line-numbers}
        {
          "device": "Device A",
           "data": {"attribute1": "value1", "attribute2": 42}
        }
        ```
    -   v1/gateway/attributes/request:
        > 向 ThingsBoard 服务器节点请求客户端或共享设备属性，将 PUBLISH 消息发送到此主题
        >![请求属性](https://github.com/shelter-rin-0810/shelter-rin/blob/main/MyPicture/Thingsboard/request_attributes.jpg?raw=true "请求属性")
        ```javascript {.line-numbers}
        {
          "id": 1, //$request_id
          "device": "Device A",
          "client": true,
          "key": "attribute1"
        }
        ```
    -   v1/gateway/telemetry:
        > 发布遥测数据，将PUBLISH消息发送到以下话题
        ```javascript {.line-numbers}
        {
          "Device A": [
            {
              "ts": 1483228800000,
              "values": {
                "temperature": 42,
                "humidity": 80
              }
            },
            {
              "ts": 1483228801000,
              "values": {
                "temperature": 43,
                "humidity": 82
              }
            }
          ],
          "Device B": [
            {
              "ts": 1483228800000,
              "values": {
                "temperature": 42,
                "humidity": 80
              }
            }
          ]
        }
        ```
    -   v1/gateway/rpc:
        > 从服务器订阅 RPC 命令，将 SUBSCRIBE 消息发送到该话题，表示期望带有以下格式的单独命令消息
        ```javascript {.line-numbers}
        {
          "device": "Device A",
          "data": {
            "id": $request_id,
            "method": "toggle_gpio",
            "params": {"pin":1}
            }
        }
        ```
        > 处理过后，网关可以使用以下格式发回命令
        ```javascript {.line-numbers}
        {
          "device": "Device A",
          "id": $request_id,
          "data": {
          "success": true
          }
        }
        ```
    -   v1/gateway/claim:
        > 启动声明设备，将 PUBLISH 消息发送到以下话题。Device A和Device B是您的设备名称，secretKey和durationMs是可选密钥。如果未指定SecretKey，则使用空字符串作为默认值。如果未指定periodMs，则使用系统参数device.claim.duration
        ```javascript {.line-numbers}
        {
          "Device A": {
            "secretKey": "value_A",
            "durationMs": 60000
          },
          "Device B": {
            "secretKey": "value_B",
            "durationMs": 60000
          }
        }
        ```

## 代码待完善
   
    