# python：requests库操作

#### access断言

```python
import time

from django.contrib.auth import login
from selenium.webdriver.common.action_chains import ActionChains
from selenium.webdriver.common.keys import Keys
from selenium import webdriver
from selenium.webdriver.common.by import By
from selenium.webdriver.support.select import Select


'''
#定义浏览器
driver = webdriver.Chrome()
#最大化窗口
driver.maximize_window()
#定义URL地址
# driver.get("http://106.52.182.140/fanwe")
driver.get("http://106.52.182.140/fanwe/m.php?m=Public&a=login&")

#隐性等待时间
driver.implicitly_wait(10)



#前台登录：
#账号
driver.find_element(By.XPATH,"//input[@id='login-email-address']").send_keys("lbw123")
# 密码
driver.find_element(By.XPATH,"//input[@id='login-password']").send_keys("123456")
# 登录按钮
driver.find_element(By.XPATH,"//input[@id='Iajax-login-submit']").click()
#跳过文本
# driver.find_element(By.CSS_SELECTOR,"#fanwe_msg_box > table > tbody > tr > td:nth-child(2) > div.dialog-button > input.dialog-cancel").click()
'''

#后台登录
# 打开浏览器
driver = webdriver.Chrome()
# 最大化浏览器
driver.maximize_window()
#输入网址
driver.get("http://106.52.182.140/fanwe/m.php?m=Public&a=login&")
#账号
driver.find_element(By.XPATH,'/html/body/form/table/tbody/tr/td[3]/table/tbody/tr[2]/td[2]/input').send_keys('admin')
#密码
driver.find_element(By.XPATH,'/html/body/form/table/tbody/tr/td[3]/table/tbody/tr[3]/td[2]/input').send_keys('admin')
# 验证码
driver.find_element(By.XPATH,'/html/body/form/table/tbody/tr/td[3]/table/tbody/tr[5]/td[2]/input').send_keys('1234')
# 登录
driver.find_element(By.ID, 'login_btn').click()
# 等待3秒，操作下个页面用
time.sleep(3)





'''
 #获取登录之后的文本信息
login=driver.find_element(By.CSS_SELECTOR,'#fanwe_msg_box > table > tbody > tr > td:nth-child(2) > div.dialog-content').get_attribute('textContent')
print("================================")
print(login)


#通过文本断言
#assert用法：assert "预期结果"  ==   实际结果, "自定义断言失败的提示"
assert"本站需绑定第三方托管账户，是否马上去绑定" == login , "断言失败，问题不对"


#通过页面标题断言
#获取页面标题
login_title = driver.title
print(login_title)
#assert语法 同上：
assert'平台'  not in  login_title,'登录失败'
'''

#对页面元素断言，执行新页面出现新元素之后才可使用
driver.switch_to.frame('top')
aa = driver.find_element(By.LINK_TEXT,'修改密码').get_attribute('href')
print(aa)
assert 'change_password' in aa ,'断言失败，不正确'

time.sleep(5)
driver.quit()
```



#### 参数为json格式的post数据传递格式

```python
# 参数为json格式的post数据传递格式
import requests
x=0
for i in range(1,100):
    url="http://1.15.24.75:8080/pinter/com/register"
    data={
        "userName":"test",
         "password":"1234",
         "gender":1,
         "phoneNum":"110",
         "email":"beihe@163.com",
         "address":"Beijing"
    }
    zhuce = requests.post(url=url,json=data)
    x+=1
    print(zhuce.text,{x})
```



#### 使用fiddler抓包 然后Nott++正则表达式替换data数据

#### 划分，在抓取的请求头中，冒号的左右分别是键值对的key跟value

```python
#使用fiddler抓包 然后Nott++正则表达式替换data数据
# 划分，在抓取的请求头中，冒号的左右分别是键值对的key跟value
import requests
jk_url = 'http://106.52.182.140/fanwe/index.php?ctl=borrow&act=savedeal&t=publish'
jk_data={
"borrowtitle":"123456u",
"imgtype":"systemImg",
"systemimgpath":"1",
"borrowtype":"1",
"is_mortgage":"0",
"borrowamount":"1000000",
"repaytime_type":"0",
"repaytime":"2324235",
"apr":"20",
"loantype":"2",
"agency_id":"0",
"warrant":"0",
"guarantor_margin_amt":"",
"guarantor_pro_fit_amt":"",
"enddate":"20",
"mortgage_desc":"",
"borrowdesc":"123123123124124",
"file_name_1":"",
"file_1":"",
"file_name_2":"",
"file_2":"",
"file_name_3":"",
"file_3":"",
"file_upload_count":"3",
}
jk_header={
    'Cookie': 'PHPSESSID=c20vhramo0ovgs7nbmhciqo5q5',
    'Content-Type':'application/x-www-form-urlencoded'
}
jk = requests.post(url=jk_url, data=jk_data,headers=jk_header)
print(jk.text)
```



#### 接口正则表达式的数据添加双引号操作

````text
背景：requests的接口如果通过抓包或者是接口文档请求的有大量的请求数据
例如：
"city_id":0
"address":
"phone":
"is_effect":1
"m":User
"a":insert
这么多的请求数据如何一键添加双引号呢？
解决方式：查找替换中的  正则表达式
解决步骤：
		0.确保key跟value之间有英文冒号：如果没有需要将空格替换为冒号：
		1.notepad++里面查找模式改为正则表达式
		2.查找目标填写(.*):(.*)$
		3.下面分情况来进行替换
			1.如果key有双引号，value没有双引号，那么填写：$1:"$2",
			2.如果key没有双引号，value有双引号，那么填写："$1":$2,
			3.如果key和value都没有双引号：那么填写："$1":"$2",
````



#### 单独上传文件

```python
#导入requests包
import requests	

#请求地址
sc_url = 'http://106.52.182.140/fanwe/file.php'	

#请求数据data
sc_tada = {
    "upload_type":"0",
    "localUrl":"C:\\fakepath\\3.png",
    "m":"File",
    "a":"do_upload"
}

#文件地址
#格式:{参数名称:(文件名,open(文件路劲,文件读的方式))}
#r:读  b:表示已二进制的格式
wj ={
    "imgfile"：("3.png",open(file='C:\\Users\\Administrator\\Desktop\\2.png',mode='rb'),'image/png')
}

#请求cookie
sc_cookie = {
    'PHPSESSID':'c20vhramo0ovgs7nbmhciqo5q5'		
}

sc = requests.post(url=sc_url,data=sc_tada,cookies=sc_cookie,files=wj)
print(sc.text)
```



#### HTTPS请求

```python
#HTTPS比HTTP多了一个SSL的收费加密的证书这个证书多数是收费的
# verify:证书参数
# 第一种：有证书文件，verify="路径\证书文件名.扩展名"(cert、cer)
# 第二种:无证书文件，可暂时忽略   verify=False
#
bd_url='https://baijiahao.baidu.com/s?id=1695026499920925716&wfr=spider&for=pc'


baidu=requests.get(url=bd_url,verify=False)

print(baidu.text)
```



#### print打印获取方式

```python
print(baidu.text)      # 支持所有格式返回
print(baidu.json())     #只支持返回json格式
print(baidu.headers)      #获取响应头
print(baidu.status_code)    #获取状态码
print(baidu.url)        #获取url，如果是3xx的就是重定向之后的状态码，其他的智能获取请求的url
print(baidu.cookies)    #获取登录成功后的cookies,可以直接使用
```



#### 动态cookies关联

```python
import requests

#登录
dl_url = 'http://106.52.182.140/fanwe/index.php?ctl=user&act=dologin&fhash=dQmBaNGMRcAjDbPDEhtkgaSWbIVDMEOJPRioWcrwiPnBLtJtqT'
dl_data={
"email":"lbw123",
"user_pwd":"Tnd2SHRHbWd4YVd6c0FYU3hLd2RKcmR5alZMWVpKREFEcHFrTmdQUm1DWmJkWllBSXklMjV1NjVCOSUyNXU3RUY0MTIzNDU2JTI1dThGNkYlMjV1NEVGNg==",
"auto_login":"0",
"ajax":"1",
}
dl=requests.post(url=dl_url,data=dl_data)
#获取登录的动态cookie
cookies=dl.cookies


#充值
cz_url = 'http://106.52.182.140/fanwe/member.php?ctl=uc_money&act=incharge_done'

cz_data={
"check_ol_bl_pay":"on",
"money":"1234",
"pingzheng":"",
"payment":"22",
"bank_id":"ICBCB2C",
"memo":"",
"bank_id":"ICBCB2C"
}

zfcg = requests.post(url=cz_url,data=cz_data,cookies=cookies)
print(zfcg.text)
```



#### 动态关联token

```python
#动态关联token
#先登录获取token值
import requests
rs_url="http://1.15.24.75:8080/pinter/bank/api/login2"
rs_data={
'userName':'lbw123',
'password':'123456'
}
rs = requests.post(url=rs_url,data=rs_data).json()

#data就是对应的token值
rs_token = rs['data']

#查询余额 （基于登录状态，需要token验证，通过请求头发送token）
cx_url='http://1.15.24.75:8080/pinter/bank/api/query2?userName=lbw123&_=1653986872299'
header={
'testfan-token':rs_token
}
cx = requests.get(url=cx_url,headers=header)
print(cx.text)
```



#### 使用session发送请求，需要创建session实例，用来记录鉴权身份信息，作用等同于cookie

```python
#登录
#实例化session
import requests
fw_session=requests.session()

dl_url = 'http://106.52.182.140/fanwe/index.php?ctl=user&act=dologin&fhash=dQmBaNGMRcAjDbPDEhtkgaSWbIVDMEOJPRioWcrwiPnBLtJtqT'
dl_data={
"email":"lbw123",
"user_pwd":"Tnd2SHRHbWd4YVd6c0FYU3hLd2RKcmR5alZMWVpKREFEcHFrTmdQUm1DWmJkWllBSXklMjV1NjVCOSUyNXU3RUY0MTIzNDU2JTI1dThGNkYlMjV1NEVGNg==",
"auto_login":"0",
"ajax":"1",
}
login = fw_session.post(url=dl_url,data=dl_data).json()
print(login)

#充值
cz_url = 'http://106.52.182.140/fanwe/member.php?ctl=uc_money&act=incharge_done'
cz_data={
"check_ol_bl_pay":"on",
"money":"1234",
"pingzheng":"",
"payment":"22",
"bank_id":"ICBCB2C",
"memo":"",
"bank_id":"ICBCB2C"
}
cz = fw_session.post(url=cz_url,data=cz_data)	#使用session 不需要在调用cookie
print(cz.text)
```



#### 方维登录平台

http://106.52.182.140/fanwe/  #登录平台

http://106.52.182.140/fanwe/m.php  #管理平台

```python
import requests
import random
while True:
    num = random.randint(10000000,999999999)
    #生成随机字符,[生成的随机字符在列表中]
    aa = random.sample('asdfghjklzxcvbnmqwertyuiop1234567890', 6)
    #使用"".join()拼接字符
    name = "".join(aa)
    zc_url ='http://106.52.182.140/fanwe/index.php?ctl=user&act=doregister'
    zc_data={
    'user_type':'0',
    'user_name':name,  #
    'mobile':'139'+str(num),
    'user_pwd':'a123456',
    'user_pwd_confirm':'a123456',
    'referer':'',
    'agreement':'1',
    'commit':'注册'
    }
    zc = requests.post(zc_url, data=zc_data)
    # coo=zc.cookies
    # print(zc.text)
```



#### 文件参数化

```python 
#文件参数化
dl_url='http://106.52.182.140/fanwe/index.php?ctl=user&act=dologin&fhash=YfljQkMemSliKbZTLBsFXPocBqmpDUsCsOWyKxpOvLvBbfHAfh'
#使用xlrd打开文件
book=xlrd.open_workbook('C:\\Users\\Administrator\Desktop\\fw.xls')
#读取数据回来，返回的数据为str格式的数据
sheet=book.sheet_by_index(0)
fw_data=sheet.cell_value(0,0)
# 打印数据类型
print(fw_data)
print(type(fw_data))
'''
#文件参数化：从xls表格中取数据回来
#表格数据时需要带{}的字典格式的数据
#把str转换成json格式 （json格式为字典）
#字典格式，键值对都应该有双引号
#json转换，不能出现单引号，应该使用英文双引号
#最后一对键值对后面不要多出其他的符号
#使用json转换的话，不能使用None，需要统一的更换成null
'''
data = json.loads(fw_data)
fwdl=requests.post(dl_url,data=data)
coo=fwdl.cookies
print(fwdl.json())
```



#### 断言

```python
#断言

fw_session=requests.session()
dl_url = 'http://106.52.182.140/fanwe/index.php?ctl=user&act=dologin&fhash=dQmBaNGMRcAjDbPDEhtkgaSWbIVDMEOJPRioWcrwiPnBLtJtqT'
dl_data={
"email":"lbw123",
"user_pwd":"Tnd2SHRHbWd4YVd6c0FYU3hLd2RKcmR5alZMWVpKREFEcHFrTmdQUm1DWmJkWllBSXklMjV1NjVCOSUyNXU3RUY0MTIzNDU2JTI1dThGNkYlMjV1NEVGNg==",
"auto_login":"0",
"ajax":"1",
}
login = fw_session.post(url=dl_url,data=dl_data).json()
#assert '预期结果'  对比 '实际结果' ,'断言失败后的提示'
assert '本站需绑定第三方托管账户，是否马上去绑定' == login['info'],'登录失败'
assert '本站需绑定第三方托管账户' in login['info'],'登录失败'
print(login)
```

