|需求编号|描述|
|--|--|
|SWS_Can_00370|函数Can_Mainfunction_Mode将轮询CAN状态寄存器的一个标志，直到该标志发出更改生效的信号，并使用函数CanIf_ControllerModeIndication通知上层成功的状态转换，引用了带有抽象CanIf ControllerId的对应CAN控制器|
|SWS_Can_00372|如果标志表示更改不起作用，并且超过了CanTimeoutDuration的最大时间，那么函数Can_SetControllerMode应该被保留，函数Can_Mainfunction_Mode应该继续轮询标志。|
|SWS_Can_00373|Can_Mainfunction_Mode函数应该调用CanIf_ControllerModeIndication函数来通知上层由抽象CanIf ControllerId引用的对应CAN控制器的状态转换成功，以防状态转换被Can_SetControllerMode函数触发。|
|SWS_Can_00271|在硬件唤醒(由 CAN 总线的唤醒事件触发)时，CAN 模块将在中断上下文或 CAN_Mainfunction_Wakeup 上下文中调用函数 Can_MainFunction_Wakeup|
|SWS_Can_00056|在第10章中标记为' multi ' (' M '或' x ')的build后配置元素可以通过将指针' Config '传递给模块的init函数来选择|
|	SWS_Can_00441|CAN_MainFunction_WRITE()的接口名称应遵循以下格式<br/> Can_MainFunction_Wrtte_0() <br/> Can_MainFunction_Write_1() <br/> Can_MainFunction_Write_2() <br/> Can_MainFunction_Write_3()|
|SWS_Can_00442	|CAN_MainFunction_Read()的接口名称应遵循以下格式：<br/> Can_MainFunction_Read_0() <br/> Can_MainFunction_Read_1() <br/> Can_MainFunction_Read_2() <br/> Can_MainFunction_Read_3()|
|SWS_CAN_00481|如果所请求的ConfigurationId不为0，则函数CAN_SetIcomConfiguration()将使用CanIcomConfigent容器的ICOM域配置参数来重新配置控制器，其中CanIcomConfigId与所请求的ConfigurationId匹配|