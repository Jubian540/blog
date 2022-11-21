# C# MQTT（MQTTnet）客户端

MQTTnet 是一个高性能 .NET 库，用于基于 MQTT 的通信。 它提供了一个 MQTT Client和一个 MQTT Server（代理）。

## 安装MQTTnet

在Visual Studio中点击`工具->NuGet 包管理器->管理解决方案的NuGet程序包`

![](https://blogfiles121.oss-cn-hangzhou.aliyuncs.com/img/UCLYNVX_FWVI%5DA8AXH%7B7Y1L.png)

搜索MQTTnet安装到项目中

![](https://blogfiles121.oss-cn-hangzhou.aliyuncs.com/img/install_MQTTnet.png)

## 客户端有关的几个重要的类

### MqttFactory

这个可以说是MQTTnet中最重要的一个类了，主要用来生成下面一些类用。

### MqttClinet

代表Mqtt客户端类，由MqttFactory生成。

### MqttClientOptions

用于配置Mqtt客户端参数（id，用户名密码等）。

### MqttClientSubscribeOptions

用于配置Mqtt订阅参数，定和约的Topic，超时时间等。  

### MqttApplicationMessage

用于描述Mqtt消息。

## 客户端开发流程

1.构建MqttFactory

2.使用MqttFactory生成MqttClient

3.生成MqttOptions绑定到MqttClient

4.使用MqttClient类连接服务器

5.使用MqttFactory生成MqttClientSubscribeOptions绑定到MqttClient

6.构建MqttApplicationMessage向服务器推送消息

## 例程

```csharp
MqttFactory myMqttFactory = new MqttFactory();
try
{
    myMqttClient = myMqttFactory.CreateMqttClient() as MqttClient;
    options = new MqttClientOptionsBuilder().WithWebSocketServer("127.0.0.1:8083/mqtt")
                    .WithCredentials("device", "123456")
                    .WithClientId(Guid.NewGuid().ToString())
                    .Build();
    options.KeepAlivePeriod = TimeSpan.FromSeconds(1);
    myMqttClient.ApplicationMessageReceivedAsync += e =>
    {
        MessageBox.Show(e.ApplicationMessage.Topic);
        MessageBox.Show(Encoding.UTF8.GetString(e.ApplicationMessage.Payload));
        return Task.CompletedTask;
    };
    await myMqttClient.ConnectAsync(options, CancellationToken.None);

    mySubscription = myMqttFactory.CreateSubscribeOptionsBuilder()
    .WithTopicFilter(
        f =>
        {
            f.WithTopic("mqttnet/topic/message");
            var publish = new MqttApplicationMessageBuilder().Build();
        })
        .Build();
        await myMqttClient.SubscribeAsync(mySubscription, CancellationToken.None);
                
        }
catch (Exception ex)
{
    MessageBox.Show(ex.Message);
}
```

完整代码参考我的Github[MqttClientDemo](https://github.com/Jubian540/MqttClientDemo)










