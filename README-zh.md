# bluetti-bluetooth-lib

## 一、蓝牙通道加密方案说明

​        为解决蓝牙通信安全问题，Bluetti产品对蓝牙通道实行加密方案，蓝牙建立连接后，须使用加解密秘钥后方可进行通信。因此，在蓝牙加密产品的功能基础上，无法使用明文进行蓝牙通信，以获取Bluetti产品数据信息。

​        为解决以上问题，Bluetti提供加蓝牙通道解密模块。加解密模块提供API供外部调用，以实现蓝牙通道的加解密功能。

## 二、使用蓝牙加解密模块

### 1. 安装

​        蓝牙加解密模块有2个文件, 安装时，需把这2个文件放到python的安装目录(site-packages): 

- bluetti_crypt.py: 对蓝牙加解密模块的python封装。

- _bluetti_crypt.so: 蓝牙加解密模块库文件，实现加解密功能。

  注: Linux和MacOS环境下文件后缀为.so，windows环境为.pyd

**安装步骤**

1. 下载lib文件: [bluetti-bluetooth-lib](https://github.com/bluetti-official/bluetti-bluetooth-lib/releases/)

2. 解压压缩包；

3. 复制蓝牙加解密模块到安装目录；

4. 复制蓝牙加解密授权文件到程序运行目录下，并更改文件名称为bluetti_device_licence.csv
   
   Note: 申请蓝牙加解密授权文件时，请使用通讯板序列号。查看方法: BLUETTI APP -->设置-->关于设备-->通讯板序列号

Linux环境可使用命令安装，命令如下：

​    解压压缩包: 

​    `unzip bluetti_crypt.zip -C bluetti_crypt`

​    复制蓝牙加解密模块到安装目录: 

​    `cp -rf bluetti_crypt.py _bluetti_crypt.so /home/cloud/.local/lib/python3.12/site-packages/`

*其中安装目录需根据自己的python目录进行修改。*

### 2. 使用

​        蓝牙加解密模块提供三个API供调用，分别为：

```c++
/*!
 * @brief 蓝牙加密通道连接处理
 * @param data, 输入数据, Bluetti设备发送过来的数据
 * @param status 返回状态
            0: 初始化中
            1~2: 鉴权中
            3: 检查SN合法性
            4: 蓝牙连接完成
 * @return 成功返回加密后的数据，失败返回""
*/
std::string BluettiCrypt::ble_crypt_link_handler(std::string data, int *status);

/*!
 * @brief 加密数据
 * @param data 待加密数据
 * @return 成功返回加密后的数据，失败返回""
 */
std::string BluettiCrypt::encrypt_data(std::string data);

/*!
 * @brief 解密数据
 * @param data, 待解密数据
 * @return 成功返回解密后的数据, 失败返回""
 */
std::string BluettiCrypt::decrypt_data(std::string data);

```

- 蓝牙连接: `ble_crypt_link_handler()`为蓝牙connect后的秘钥鉴权阶段使用，此阶段完成后方可进行查询数据等交互。在秘钥鉴权阶段，无需额外处理蓝牙数据，直接透传即可；即蓝牙加解密模块和蓝牙端直接进行数据交互。直到`ble_crypt_link_handler`返回`BLE_LINK_STATUS_COMEPLETE`方可进行下一步骤。

- 蓝牙数据加密: `encrypt_data()`为数据加密API。发送到Bluetti设备的蓝牙数据，需要经过加密才可识别。

- 蓝牙数据解密: `decrypt_data()`为数据解密API。Bluetti设备的蓝牙数据为加密数据，需经过解密后才可解析协议数据。

在Home-Assistant和bluetti-mqtt开源上的使用，已提供daemo可供参考。