# 第 29 課：用 Python 發送信件與訊息

前面幾張說明如何用 Python 程式自動產生 Excel、Word、PDF 檔案，接下來還可以更進一步的透過信箱將產生出來的檔案發送給指定的收件人，再利用訊息告知對方已送出信件，這些事情可以利用 Python 輕鬆達到。

## 發送電子郵件
電子郵件仍然是現今使用最為廣泛的應用之一，公司向待聘人員發出錄取通知、網站向使用者發送一個開通帳號的連結、銀行向客戶推銷他們的理財商品，這些幾乎都是透過電子郵件來完成的，而這些任務應該都是由程式自動完成的。

我們可以使用 HTTP (超文本傳輸協定) 來訪問網站資源，HTTP 是一個應用層級的協定，建立在 TCP (傳輸控制協定) 之上，TCP 為很多應用層級協定提供了可靠的資料傳輸服務。

如果要發送電子郵件，需要使用 SMTP (簡單郵件傳輸協定)，它也是建立在 TCP 之上的應用層級協定，規定郵件的發送者如何與郵件服務器進行溝通的細節。Python 透過名為 `smtplib` 的模組將這些操作簡化成了 `SMTP_SSL` 物件，透過該物件的 `login` 和 `send_mail` 方法，就能夠完成發送信件的操作。

首先嘗試發送一個簡單的信件，該信件不包含附件、圖片以及其他內容。發送郵件首先需要連接信箱服務器，我們可以直接使用 Google 帳號將它當作一個信箱服務器。

```Python
import smtplib
from email.header import Header
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText

# 建立信件物件
email = MIMEMultipart()
# 設定發送人員、收件人員和主旨
email['From'] = 'abc@google.com'
email['To'] = 'xyz@gmail.com;triple@yahoo.com.tw'
email['Subject'] = Header('工作週報', 'utf-8')
# 加入信件內容
content = """本週工作內容如下，
1. 完成交辦事項
2. 完成交接事項
3. OJT
"""
email.attach(MIMEText(content, 'plain'))

# 建立 SMTP_SSL 物件（連接信箱服務器）
smtp_obj = smtplib.SMTP_SSL('smtp@gmail.com', 587)
# 透過使用者名稱和 google 的應用程式密碼
smtp_obj.login('abc@google.com', '應用程式密碼')
# 發送信件 (寄件人、收件人、信件內容 (字串))
smtp_obj.sendmail(
    'abc@gmail.com',
    ['xyz@gmail.com', 'triple@yahoo.com.tw'],
    email.as_string()
)
```
如果要傳送帶有附件的信件，只需要將附件的內容處里程 Base64 編碼，那麼他就跟普通的字串內容幾乎沒有什麼區別。

Base64 是一種基於 64 個可以列印字元來表示二進位資料的表示方法，常用於某些需要顯示、傳輸、儲存二進制資料的場合。

下面的代码演示了如何发送带附件的邮件。

```py
import smtplib
from email.header import Header
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from urllib.parse import quote

# 建立信件物件
email = MIMEMultipart()
# 設定寄件者、收件者和主旨
email['From'] = 'abc@gmail.com'
email['To'] = 'xyz@yahoo.com.tw'
email['Subject'] = Header('簡單傳輸資料', 'utf-8')
# 加入信件內容 (帶 HTML 標籤排版的內容）
content = """
<p>Hello：</p>
<p>World！</p>
<br>
<p>你好！</p>
<hr>
<p>世界</p>"""
email.attach(MIMEText(content, 'html', 'utf-8'))
# 讀取作為附件的檔案
with open(f'resources/你好.docx', 'rb') as file:
    attachment = MIMEText(file.read(), 'base64', 'utf-8')
    # 指定內容類型
    attachment['content-type'] = 'application/octet-stream'
    # 將中文文件名稱處裡為百分號編碼
    filename = quote('你好.docx')
    # 指定如何處裡內容
    attachment['content-disposition'] = f'attachment; filename="{filename}"'

# 建立 SMTP_SSL 物件（連接信箱服務器）
smtp_obj = smtplib.SMTP_SSL('smtp@gmail.com', 587)
# 透過使用者名稱和 google 的應用程式密碼
smtp_obj.login('abc@google.com', '應用程式密碼')
# 發送信件 (寄件人、收件人、信件內容 (字串))
smtp_obj.sendmail(
    'abc@gmail.com',
    ['xyz@gmail.com', 'triple@yahoo.com.tw'],
    email.as_string()
)
```

為了方便使用信件發送，將上面的程式碼封裝為函數，使用的時候只需呼叫伺服器網域名稱、阜號、使用者名稱與授權密碼

```py
import smtplib
from email.header import Header
from email.mime.multipart import MIMEMultipart
from email.mime.text import MIMEText
from urllib.parse import quote

# 郵件服務器網域名稱
EMAIL_HOST = 'smtp.gmail.com'
# 郵件服務阜號
EMAIL_PORT = 587
# 登入郵件服務器帳號
EMAIL_USER = 'abc@gmail.com'
# 開通 SMTP 服務器的密碼
EMAIL_AUTH = '密碼'

def send_email(*, from_user, to_users, subject='', content='', filenames=[]):
    """發送信件
    
    :param from_user: 寄件者
    :param to_users: 收件人，多個收件者使用英文分號進行分隔
    :param subject: 信件主旨
    :param content: 信件內容
    :param filenames: 附件檔案的路徑位置
    """
    email = MIMEMultipart()
    email['From'] = from_user
    email['To'] = to_users
    email['Subject'] = subject

    message = MIMEText(content, 'plain', 'utf-8')
    email.attach(message)
    for filename in filenames:
        with open(filename, 'rb') as file:
            pos = filename.rfind('/')
            display_filename = filename[pos + 1:] if pos >= 0 else filename
            display_filename = quote(display_filename)
            attachment = MIMEText(file.read(), 'base64', 'utf-8')
            attachment['content-type'] = 'application/octet-stream'
            attachment['content-disposition'] = f'attachment; filename="{display_filename}"'
            email.attach(attachment)

    smtp = smtplib.SMTP_SSL(EMAIL_HOST, EMAIL_PORT)
    smtp.login(EMAIL_USER, EMAIL_AUTH)
    smtp.sendmail(from_user, to_users, email.as_string())
```

## 發送訊息
發送訊息也是常見的功能，網站的註冊碼、驗證碼、經營訊息基本上都是透過訊息來發送給使用者的。

需要找到一個發送訊息的平台。

接下來，透過 `requests` 函式庫像平台發送 HTTP 請求，透過將接收訊息的手機號碼和訊息內容作為參數，就可以發送訊息。

```py
import random

import requests


def send_message_by_fake(tel, message):
    """發送訊息"""
    resp = requests.post(
        url='http://sms-api.fake.com/v1/send.json',
        auth=('api', 'key-註冊成功後所提供的金鑰 KEY'),
        data={
            'mobile': tel,
            'message': message
        },
        timeout=10,
        verify=False
    )
    return resp.json()


def gen_mobile_code(length=6):
    """產生指定長度的手機驗證碼"""
    return ''.join(random.choices('0123456789', k=length))


def main():
    code = gen_mobile_code()
    message = f'你的訊息驗證碼{code}，！'
    print(send_message_by_fake('13500112233', message))


if __name__ == '__main__':
    main()
```

上面發送請求會回傳 JSON 格式的資料
