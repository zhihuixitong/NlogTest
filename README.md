# NlogTest
.net6+nlog测试

使用步骤

1.安装组件
NLog.Extensions.Logging

2.在项目下新增nlog.config文件（文件可能不会及时生成道打包或者调试文件中，需要属性设置始终复制）

具体格式文档：https://nlog-project.org/config/?tab=layout-renderers

```xaml
<?xml version="1.0" encoding="utf-8" ?>
<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	<targets>
		<target name="logconsole" xsi:type="Console"
			 useDefaultRowHighlightingRules="false">
			<layout type='JsonLayout' suppressSpaces='true' EscapeForwardSlash='true'>
				<attribute name='appId' layout='NlogTest' />
				<attribute name='programId' layout='${processname}'/>
				<attribute name='instanceName' layout='${hostname}'/>
				<attribute name='time' layout='${longdate}'/>
				<attribute name='source' layout='${logger}'/>
				<attribute name='subject' layout=''/>
				<attribute name='level' layout='${level}'/>
				<attribute name='content' layout='${message}'  encode='true' EscapeUnicode='false' />
				<attribute name='ipAddress' layout='${local-ip}'/>
				<attribute name='macAddress' layout=''/>
				<attribute name='traceId' layout='${activity:property=TraceId}'/>
			</layout>

		</target>
	</targets>
	<rules>
		<logger name="NlogTest*" levels="Trace,Debug,Info,Error,Fatal" writeTo="logconsole" />
	</rules>
</nlog>
```



3.Program.cs注入

```c#
builder.Services.AddLogging(nlog =>
{
    nlog.ClearProviders();
    nlog.AddNLog();
});
```

4.调用接口输出日志

```c#
private readonly ILogger<WeatherForecastController> _logger;

_logger.LogInformation("输出日志！！");

```

```json
输出日志：
{"appId":"NlogTest","programId":"NlogTest","instanceName":"DESKTOP-JM97E2E","time":"2023-03-31 09:27:29.9296","source":"NlogTest.Controllers.WeatherForecastController","level":"Info","content":"输出日志！！","ipAddress":"192.168.1.12"}
```



5.问题日志输出过多会有性能问题

6.支持数据库保存日志（未实现）
