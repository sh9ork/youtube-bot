# youtube-bot
import os
import telebot
import youtube_dl

bot = telebot.TeleBot("23423546:AGVNDUFYGhfshdfbsd")


@bot.message_handler(commands=['start'])
def shoot(message):
    bot.send_message(message.chat.id, "Gimme YouTube link")


@bot.message_handler()
def run(message):
    if "https://www.youtube.com" not in message.text:
        print("This is not YouTube link!")
        return

    bot.send_message(message.chat.id, "Please wait...")

    video_info = youtube_dl.YoutubeDL().extract_info(
        url=message.text, download=False
    )
    filename = f"{video_info['title']}.mp3"
    options = {
        'format': 'bestaudio/best',
        'keepvideo': False,
        'outtmpl': filename,
    }

    with youtube_dl.YoutubeDL(options) as ydl:
        ydl.download([video_info['webpage_url']])

    print("Download complete... {}".format(filename))
    bot.send_audio(message.chat.id, audio=open(filename, 'rb'))


bot.polling()
