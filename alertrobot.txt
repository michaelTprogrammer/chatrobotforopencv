# chatrobotforopencv
The terminal will send message to user when the robot detect something on the ground in supermaket
from flask import Flask, request, abort

from linebot import (
    LineBotApi, WebhookHandler
)
from linebot.exceptions import (
    InvalidSignatureError
)
from linebot.models import (
    MessageEvent, TextMessage, TextSendMessage,
)

app = Flask(__name__)

line_bot_api = LineBotApi('iEBWH1zWqSdYm8hlKcSSdphSFzxYM1DOJl+vfhyQBL2KsVkeKjJkG/sEQ+bdc/1kkrkY4xO/KZzAN6m400C7zyEFar4ZNLHJK1qJHYMkcLaXHDQPW4oJmz2Kr7JoZ13Fjl+BqfU8jNzPtv8SjPFb7wdB04t89/1O/w1cDnyilFU=')
handler = WebhookHandler('89ec19ba634463260c96d62d49114865')


@app.route("/callback", methods=['POST'])
def callback():
    # get X-Line-Signature header value
    signature = request.headers['X-Line-Signature']

    # get request body as text
    body = request.get_data(as_text=True)
    app.logger.info("Request body: " + body)

    # handle webhook body
    try:
        handler.handle(body, signature)
    except InvalidSignatureError:
        print("Invalid signature. Please check your channel access token/channel secret.")
        abort(400)

    return 'OK'


@handler.add(MessageEvent, message=TextMessage)

def handle_message(event):
    if event.source.user_id =='U16599adb39ed4c414474eadff450242d': #填入line在verify時回傳的user_id
        return 'OK'
    line_bot_api.reply_message(
        event.reply_token,
        TextSendMessage(text='HELLO,Please wait for the message'))
    

def handle_camera_event():
    # 定義觸發條件
    # ID列表，例如：
    user_ids = ['Uf050c91869334f4f96422295465a7797']  # LINE Developers上的your ID
    # 向每個好友推消息
    for user_id in user_ids:
        line_bot_api.push_message(to=user_id, messages=TextSendMessage(text='發現異物，立即出動'))

if __name__ == "__main__":
    app.run(debug=True)
