python一直被詬病執行速度 除了可以透過套件去提高速度 電腦CPU提高有幫助嗎?
現在來測試一下

首先我們要先知道自己的CPU實際核心是多少 以我這台i5-9400F 6核6緒 如果不設定執行核心要多久?

這個Code是用單核方式去執行

import os 
import time

def longTime(i):
    print('PID: {}, Task: {}'.format(os.getpid(), i))
    time.sleep(2)
    result=10*30
    print('Result:{}'.format(result))
if __name__=="__main__":
    start=time.time()
    
    for i in range(0,4):
        longTime(i)
    
    end=time.time()
    print("總共花費 {} 秒".format(end-start))

而這個code是用    4核去執行

import multiprocessing as mp
import os 
import time

def longTime(i):
    print('PID: {}, Task: {}'.format(os.getpid(), i))
    time.sleep(2)
    result=10*30
    print('Result:{}'.format(result))
if __name__=="__main__":
    start=time.time()
    print('母程序PID: {}'.format(os.getpid()))
    
    w1=mp.Process(target=longTime,args=(1,))
    w2=mp.Process(target=longTime,args=(2,))
    w3=mp.Process(target=longTime,args=(3,))
    w4=mp.Process(target=longTime,args=(4,))
    
    w1.start()
    w2.start()
    w3.start()
    w4.start()
    
    w1.join()
    w2.join()
    w3.join()
    w4.join()    
    
    end=time.time()
    print("總共花費 {} 秒".format(end-start))

前面提到了我是6核心 因此分別用 單核 4、6核 、8核、12核 去執行產生出下面結果
