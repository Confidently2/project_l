#代码
importsocket
import time
import os

#引脚48定义
value_no48 = "0"
value_yes48 = "1"
gpio_value48_path = "/sys/class/gpio/gpio48/value"

#引脚49定义
value_no49 = "0"
value_yes49 = "1"
gpio_value49_path = "/sys/class/gpio/gpio49/value"

#引脚50定义
value_no50 = "0"
value_yes50 = "1"
gpio_value50_path = "/sys/class/gpio/gpio50/value"

#引脚51定义
value_no51 = "0"
value_yes51 = "1"
gpio_value51_path = "/sys/class/gpio/gpio51/value"

#字体颜色定义
RED = "\033[31m"
RESET = "\033[0m"

def main():
# 设置服务器的IP地址和端口号
    server_ip = '192.168.1.116'
    server_port = 1000
    
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)

# 绑定IP地址和端口号
    server_socket.bind((server_ip, server_port))

# 开始监听
    server_socket.listen(5)
    print(f"服务器启动，等待连接...")

    try:
# 接受连接
        while True:
            client_socket, addr = server_socket.accept()
            print(f"接收到来自 {addr} 的连接")
# 接收数据
            while True:
                data = client_socket.recv(1024)
                if not data:
# 如果没有接收到数据，跳出循环
                    print(f"请稍后正在继续连接")
                    break

# 将字节数据解码为字符串
                message = data.decode('utf-8')

# 打印原始接收到的数据
                print("接收到的数据:", message)

# 假设接收到的数据是以逗号分隔的值
                labels = ["湿度","温度","PM1.0","PM2.5","PM10","2.5um","5.0um","10um","传输命令"]
                values = message.split(',')

# 单独打印每个值
                for i, value in enumerate(values, start=1):
                    if i<= len(labels):
                        label = labels[i-1]
                    val = float(value.strip())
                    #if i == 1 and  val < 50  :
                        #print(f"{RED}{label}: {val}{RESET}")
                        #with open(gpio_value51_path,'w')as gpio_fil:
                            #gpio_fil.write(value_no51)
                    if i == 2 and val > 35:
                        print(f"{RED}{label}: {val}{RESET}")
                    elif i == 4 and 0 < val > 8 :
                        print(f"{RED}{label}:{val}{RESET}")
                        with open(gpio_value51_path,'w')as gpio_fil:
                            gpio_fil.write(value_no51)
                    elif i == 9 and val == 2:
                        with open(gpio_value48_path,'w')as gpio_fi:
                            gpio_fi.write(value_no48)
                        with open(gpio_value50_path,'w')as gpio_q:
                            gpio_q.write(value_yes50)
                        with open(gpio_value49_path,'w')as gpio_f:
                            gpio_f.write(value_yes49)
                    elif i == 9 and val == 1:
                        with open(gpio_value50_path,'w')as gpio_q:
                            gpio_q.write(value_no50)
                        with open(gpio_value49_path,'w')as gpio_f:
                            gpio_f.write(value_yes49)
                        with open(gpio_value48_path,'w')as gpio_fi:
                            gpio_fi.write(value_yes48)
                    elif i == 9 and val == 3:
                        with open(gpio_value49_path,'w')as gpio_f:
                            gpio_f.write(value_no49)
                        with open(gpio_value50_path,'w')as gpio_q:
                            gpio_q.write(value_yes50)
                        with open(gpio_value48_path,'w')as gpio_fi:
                            gpio_fi.write(value_yes48)
                    elif i == 9 and val == 8:
                        with open(gpio_value51_path,'w')as gpio_fil:
                            gpio_fil.write(value_no51)
                    elif i == 9 and val == 7:
                        with open(gpio_value51_path,'w')as gpio_fil:
                            gpio_fil.write(value_yes51)
                    else:
                        print(f"{label}:{val}") 
                        
# 打印完毕后等待5秒
                for seconds in range(1,0,-1):
                    print(f"\r等待{seconds}秒后继续接收数据",end='')
                    time.sleep(1)

    except KeyboardInterrupt:
# 允许使用Ctrl+C来中断服务器
        print("\n服务器正在关闭...")

    finally:
# 关闭连接
        server_socket.close()
        print("服务器已关闭。")

if __name__ == '__main__':
    main()
