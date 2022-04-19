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

[Create credentials](https://developers.google.com/workspace/guides/create-credentials)

[A1 notation](https://developers.google.com/sheets/api/guides/concepts)

[use python to handle spreadsheet](https://developers.google.com/sheets/api/quickstart/python)
