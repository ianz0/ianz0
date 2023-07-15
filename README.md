<?php

// تضمين ملف الكلاسات الخاص بالبوت
require 'vendor/autoload.php';

use Telegram\Bot\Api;

// تعيين توكن البوت الخاص بك
$token = 'YOUR_BOT_TOKEN';

// إنشاء كائن من البوت
$telegram = new Api($token);

// استلام تحديثات التيليجرام
$updates = $telegram->getWebhookUpdates();

// استجابة للرسائل المستلمة
foreach ($updates as $update) {
    $message = $update->getMessage();
    $chatId = $message->getChat()->getId();
    $text = $message->getText();

    if ($text === '/start') {
        $telegram->sendMessage([
            'chat_id' => $chatId,
            'text' => 'مرحبًا بك في بوت تلكرام!'
        ]);
    } elseif ($text === '/hello') {
        $telegram->sendMessage([
            'chat_id' => $chatId,
            'text' => 'مرحبًا، كيف يمكنني مساعدتك؟'
        ]);
    } elseif ($text === '/date') {
        $date = date('Y-m-d');
        $telegram->sendMessage([
            'chat_id' => $chatId,
            'text' => 'التاريخ الحالي هو: ' . $date
        ]);
    } else {
        $telegram->sendMessage([
            'chat_id' => $chatId,
            'text' => 'عذرًا، لم أتمكن من فهم طلبك.'
        ]);
    }
}
