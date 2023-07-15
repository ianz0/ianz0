import os
import img2pdf
from telegram.ext import Updater, MessageHandler, Filters

# تعيين توكن البوت الخاص بك
TOKEN = '5743428061:AAEXK13NbHACvzsW9DsZ334KP8Qm-67ugMg'

# تعيين المسار المؤقت لحفظ الصور المستلمة
TEMP_DIR = 'temp'

# إعداد المسار المؤقت
os.makedirs(TEMP_DIR, exist_ok=True)


def convert_image_to_pdf(image_path, pdf_path):
    # تحويل الصورة إلى ملف PDF
    with open(pdf_path, "wb") as f:
        f.write(img2pdf.convert(image_path))


def handle_image(update, context):
    # استلام الصورة المرسلة من المستخدم
    photo = update.message.photo[-1]
    file_id = photo.file_id
    file_path = context.bot.get_file(file_id).file_path
    image_path = os.path.join(TEMP_DIR, f'{file_id}.jpg')
    pdf_path = os.path.join(TEMP_DIR, f'{file_id}.pdf')

    # تنزيل الصورة وحفظها مؤقتًا
    context.bot.get_file(file_id).download(image_path)

    # تحويل الصورة إلى ملف PDF
    convert_image_to_pdf(image_path, pdf_path)

    # إرسال الملف PDF إلى المستخدم
    context.bot.send_document(
        chat_id=update.message.chat_id,
        document=open(pdf_path, 'rb'),
    )

    # حذف الملفات المؤقتة
    os.remove(image_path)
    os.remove(pdf_path)


def main():
    # إعداد محرك التحديث وتعيين المعالج الخاص بالصور
    updater = Updater(TOKEN, use_context=True)
    dp = updater.dispatcher
    dp.add_handler(MessageHandler(Filters.photo, handle_image))

    # بدء تشغيل البوت
    updater.start_polling()
    updater.idle()


if __name__ == '__main__':
    main()
