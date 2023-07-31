# input_mpu6050
mpu6050之input子系统开发

## 驱动开发步骤
init或probe函数中：
1. 创建struct input_dev对象input_allocate_device
2. 设置事件类型以及相关参数set_bit
3. 注册struct input_dev对象input_register_device

exit或remove函数中：
1. 注销struct input_dev对象input_unregister_device
2. 销毁struct input_dev对象input_free_device

上报事件
两种事件上报方式：
1. 对有中断支持的输入设备：在其中断处理函数（上半部或下半部）中上报事件
2. 对无中断支持的输入设备：使用workqueue循环定时上报(struct delayed_work)

主要函数：
1. input_event
2. input_report_abs
3. input_sync


### 相关接口：
```c
/*_init*/
struct input_dev *input_allocate_device(void)//创建对象

void set_bit(struct input_dev *dev,unsigned long whichbits)//设置事件类型

void input_set_abs_params(struct input_dev *dev,unsigned int axis,int min,int max,int fuzz,int flat)

int input_register_device(struct input_dev *dev)//注册input设备到内核

/*_exit*/
void input_unregister_device(struct input_dev *dev)
void input_free_device(struct input_dev *dev)

/*上报事件*/
void input_event(struct input_dev *,unsigned int t,unsigned int c,int v)

void input_report_key(struct input_dev *,unsigned int c,int v) //上报按键事件
void input_report_abs(struct input_dev *,unsigned int c,int v)//上报绝对坐标事件
    
void input_sync(struct input_dev *)//上报完成后需要调用这些函数来通知系统处理完整事件

/*应用层数据类型*/
struct input_event {
    struct timeval time;       // 时间戳
    __u16 type;             // 事件类型
    __u16 code;             // 哪个分值
    __s32 value;            // 具体值      
};
```
