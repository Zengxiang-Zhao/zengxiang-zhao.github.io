---
layout: default
title: 杂项
date:   2021-10-3 20:28:05 -0400
categories: pyhon
nav_order: 97

---

---
{: .no_toc }

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
1. TOC
{:toc}
</details>

---

# implement OCR on PC

You can create a OCR app directily on Linux or Windows PC. The process as below.

1. Write Python code to convert image to palin text. Firstly, you need to download tesseract(this is the engine for OCR) and pytesseract(python inferace for tesseract). This step is simple
2. use pyintstaller to generate a executable file. If you use Linux system, and want the file run on Windows. You need to install "wine" on your Linux OS. Don't foget to install the associated packages used in python code. Actually you could use ["piprequest" to get the necessary packages](/docs/python.html#use-pipreqs-to-extract-the-packages-used-in-the-script). 
3. If you'd like to generate a icon for your app. You can wirte like these to add an icon `wine pyinstaller --icon=OCR2.ico -F app_OCR.py`. Use the [online method](https://icoconvert.com/) to convert your picture to an windows icon.
4. Meanwhile if you run the software,you need to install tesseract on you Windows PC. Here is the [Windows tesseract installer](https://github.com/UB-Mannheim/tesseract/wiki#tesseract-installer-for-windows). Install it in a specific folder, because we need the "tesseract.exe" in that folder.
5. now you can change the link of pytesseract.pytesseract.tesseract_cmd like `pytesseract.pytesseract.tesseract_cmd = config['path_tesseract_cmd']`. config['path_tesseract_cmd'] is the absolute path to the "tesseract.exe"
6. Create a soft link and place it on the desktop. Now you can use the OCR technique to convert the picture to plain text

# VSCode 链接remote server

本地电脑不给力，想充分利用远端server的算力。那么可以使用VSCode，直接链接remote server。方法非常简单

1. [下载VScode](https://code.visualstudio.com/download)并安装
2. 打开VSCode并在下载界面安装Microsoft出的`Remote - SSH`插件
3. 在最左侧边栏会出现`Remote Explorer`，点击并连接
4. 你可以能也需要安装`Pylance`插件，来帮助你选择在server端的环境
5. 如果你想在VSCode中使用Jupytr Notebook，则需要在虚拟环境下安装 `conda install -n recon ipykernel --update-deps --force-reinstall`
6. 现在准备工作完成，可以开心的写代码了。

# 在Linux电脑上安装Java

1. 从官方[下载Java安装包](https://www.oracle.com/java/technologies/downloads/) : 如果是 Ubuntu 选择 “x64 Compressed Archive”
2. 把下载的文件夹放到你想放的目录下，你可以选择创建一个文件夹例如：/usr/java/
3. 解压Java压缩文件: `tar zxvf jdk-18_linux-x64_bin.tar.gz`
4. 把java目录添加到Linux Path中。可以在 `~/.zshrc` 中进行修改：`PATH=$PATH:/usr/java/jdk-18.0.1.1/bin` 。这是因为java的可执行文件放在bin中所以

# [在Linux电脑上进行zip和unzip](https://linuxize.com/post/how-to-zip-files-and-directories-in-linux/)

[zip](https://linuxize.com/post/how-to-zip-files-and-directories-in-linux/) / [unzip](https://linuxize.com/post/how-to-unzip-files-in-linux/)


`zip OPTIONS ARCHIVE_NAME FILES` like `zip archivename.zip filename1 filename2 filename3`

``` bash
zip archivename.zip filename1 filename2 filename3

zip -q archivename.zip filename1 filename2 filename3
# quiet

zip -r archivename.zip directory_name
# subdirectory in directory

# unzip

unzip latest.zip

unzip -q filename.zip
# quiet

unzip filename.zip -d /path/to/directory
# to another folder

```


# 在Linux电脑上安装wine来运行Windows软件

以下以在Ubuntu OS上安装wine，使用wine安装python3.8，然后使用pyinstaller生成可以在windows上运行的脚本软件

1. 在Ubuntu上安装wine ： `sudo apt-get install wine`
2. 下载windows运行的software . 例如下载python ： `wget https://www.python.org/ftp/python/3.8.10/python-3.8.10-amd64.exe`
3. 安装exe文件 `wine some.exe` ： `wine python-3.8.10-amd64.exe`。可以customize the insntallation process
4. 安装 pyinstaller ：`wine pip install pyinstaller`
5. 使用pyinstaller生成可运行的windows software ： `wine pyinstaller test.py`

# 更改GitHub personal token

## [创建新的Personal Token](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token)

## 在电脑上更新

1. 使用 `command+ 空格` 搜索 Keychain Access
2. 在弹出界面中搜索`github`
3. 在github login 这一行选中，右键，删除。
4. 在Terminal端进行push。填入Username ， 在Password选项粘贴personal Token。
5. 完成


# 引用本地link

图可参考[python知识点](/docs/plot_graph#pltbar)

直接以`/docs/...`开头，后面如果不确定可以在web中点开查看后面内容。


# 写公式

`<img src="https://render.githubusercontent.com/render/math?math=\| w \|_2^2 = \sum_{j=1}^{m}w_j^2
">`

在math后面写你想写的公式。如果有些符号不确定可参考[Latext pdf 文档](https://www.caam.rice.edu/~heinken/latex/symbols.pdf)

## 在公式中写加号+ 

在GitHub markdown 的公式中填写加号+，使用`%2B`。

<img src="https://render.githubusercontent.com/render/math?math=F1 = 2 \frac{PRE \times REC}{PRE %2B REC}
">

# [install nodejs from source file](https://stackoverflow.com/questions/63312642/how-to-install-node-tar-xz-file-in-linux)

```bash
sudo cp -r node-v14.15.5-linux-x64/{bin,include,lib,share} /usr/
```
其中核心思想就是把想要安装的package中的bin,include,lib,share 文件夹中的内容放到你当前env下的相同文件夹中。可使用 ·which node·来查看当前node安装的路径。
然后把 /usr/ 换成env下的路径。此方法可以延伸到手动安装其他的package

# python 在terminal输出颜色标记的文字

```python

from datetime import datetime

class bcolors:
    HEADER = '\033[95;1m'
    OKBLUE = '\033[34;1m'
    OKCYAN = '\033[35m'
    OKGREEN = '\033[32;1m'
    WARNING = '\033[91m'
    FAIL = '\033[31;4m'
    ENDC = '\033[0m'
    BOLD = '\033[1m'
    UNDERLINE = '\033[4m'
    RED = "\033[31;3m"
    GREEN = '\033[32;1m'

def message(message,color=bcolors.OKGREEN):
    """print the message using color and time stamp
    color : bcolors
    message : string
    """
    timeStamp = datetime.now().strftime('%Y-%m-%d %I:%M%p')

    print(f'[{timeStamp}] : {color}{message}{bcolors.ENDC}')
   
```

![color word](/assets/images/other/color_word.png)

# 如何建立Google API

首先需要在Google Developers Console中创建或选择一个项目，然后自动打开API。这一步操作可参考[python 读取Gmail](https://justcode.ikeepstudying.com/2019/09/python-%E8%AF%BB%E5%8F%96gmail-python-%E6%90%9C%E7%B4%A2gmail-python%E6%93%8D%E4%BD%9Cgmail-how-to-access-gmail-using-python/). 

`Navigation Menu -> IAM & Admin -> Creat a Project -> write your project name and hit create`. Next step is to enable the APIs that you want to use.

## 链接 Gmail

Chose the Project name near Google Cloud Platform, Then choose `Enable APIs & services` and then hit `+ENABLE APIS AND SERVICES`. Now you can search the API name, such as "Gmail API" or "Google Sheets API". Hit Enable, now the API is enabled. The next step is to add CREDENTIALS. Hit CREDENTIALS and `+ CREATE CREDENTIALS` to select 'OAuth client ID' -> Configure consent screent -> internal -> create -> write the App information, fill all the asterisk cell.

If you are in the newly created project, you need to configure the consent screen at first. After that, hit Enabled APIs & services to add and enalbe APIs. Search the APIs and enable.

Download the credential, if there are some token.json that revoked or despired. You need to delete them first, and use the newly gnerated credential.json to log in your gmail account to generate new token.json files.

All Done!


# 使用Google API 和 Python处理spreadsheet


[Create credentials](https://developers.google.com/workspace/guides/create-credentials)

[A1 notation](https://developers.google.com/sheets/api/guides/concepts)

[use python to handle spreadsheet](https://developers.google.com/sheets/api/quickstart/python)

```python
import os
import pandas as pd
import os.path

from google.auth.transport.requests import Request
from google.oauth2.credentials import Credentials
from google_auth_oauthlib.flow import InstalledAppFlow
from googleapiclient.discovery import build
from googleapiclient.errors import HttpError


from utils import bcolors, message

SCOPES = ['https://www.googleapis.com/auth/spreadsheets.readonly']


def gsheet_api_check(path_json_cred,SCOPES):
    """Check and create credential
    Parameters:
    ---------
    - path_json_cred : str
    - SCOPES : str
        like : ['https://www.googleapis.com/auth/spreadsheets.readonly']
    
    Reuturns:
    ---------
    - creds 
    
    """
    creds = None
    if os.path.exists('token_spreadsheet.json'):
        creds = Credentials.from_authorized_user_file('token_spreadsheet.json', SCOPES)

    if not creds or not creds.valid:
        if creds and creds.expired and creds.refresh_token:
            creds.refresh(Request())
        else:
            flow = InstalledAppFlow.from_client_secrets_file(path_json_cred,SCOPES)
            creds = flow.run_local_server(port=0)

        with open('token_spreadsheet.json','w') as token:
            token.write(creds.to_json())
            


    return creds


def pull_sheet_data(creds,SPREADSHEET_ID,DATA_TO_PULL):
    """Extract values from spreadsheet
    Parameters:
    ---------
    - creds : output of gsheet_api_check
    - SPREADSHEET_ID : str
    - DATA_TO_PULL : str, A1 notation (https://developers.google.com/sheets/api/guides/concepts)
     like : 'Sheet1!A:A'
     
     Returns:
     ---------
     - values : List
    
    """
    service = build('sheets', 'v4', credentials=creds)
    sheet = service.spreadsheets()
    result = sheet.values().get(
        spreadsheetId=SPREADSHEET_ID,
        range=DATA_TO_PULL,majorDimension = "COLUMNS").execute()
    values = result.get('values', [])

    if not values:
        message(f'No data found in {DATA_TO_PULL}',bcolors.FAIL)
    else:
        return values
    
def convert_spreadsheet2df(spreadsheet_id,sheet_name,list_columns, columns_name=None):
    """Get data from spreadsheet and convert the data to dataframe
    Paramerters:
    ---------
    - spreadsheet_id : str
    - sheet_name : str
    -list_columns: list of string
        like ['A','B','E']
    - columns_name : list of string
        like ['name','age','sex']
    
    """
    list_df = []
    length = None
    for c,n in zip(list_columns,columns_name):
        data_range = f'{sheet_name}!{c}:{c}'
        data = pull_sheet_data(creds,spreadsheet_id,data_range)
        df = pd.DataFrame({n:data[0][1:]})
        list_df.append(df)


    df_concat = pd.concat(list_df,axis=1)    

    return df_concat

```

