<?php
/**
 * بروكسي بسيط لـ Numverify — آمن للنشر على GitHub.
 *
 * المفتاح مش مكتوب في الكود، بيتقرأ من متغير بيئة (environment variable)
 * اسمه NUMVERIFY_KEY. كده تقدر تنشر الريبو عام من غير ما مفتاحك يبان.
 *
 * إزاي تحط المفتاح:
 *   - لوكال: عدّل php.ini أو شغّل: NUMVERIFY_KEY=xxxx php -S localhost:8000
 *   - Apache: ضيف السطر ده في .htaccess أو vhost:
 *       SetEnv NUMVERIFY_KEY xxxx
 *   - استضافات زي Render/Railway/Vercel: من لوحة Environment Variables
 *
 * الاستخدام من الفرونت:
 *   fetch('/proxy.php?number=201001234567&country_code=EG')
 */

header('Content-Type: application/json; charset=utf-8');
header('Access-Control-Allow-Origin: *');

$ACCESS_KEY = getenv('NUMVERIFY_KEY');

$number = isset($_GET['number']) ? trim($_GET['number']) : '';
$countryCode = isset($_GET['country_code']) ? trim($_GET['country_code']) : '';

if ($number === '') {
    http_response_code(400);
    echo json_encode([
        'success' => false,
        'error' => ['info' => 'من فضلك ابعت رقم الهاتف عن طريق ?number=']
    ]);
    exit;
}

if (!$ACCESS_KEY) {
    http_response_code(500);
    echo json_encode([
        'success' => false,
        'error' => ['info' => 'متغير البيئة NUMVERIFY_KEY مش متحدد على السيرفر.']
    ]);
    exit;
}

$params = [
    'access_key' => $ACCESS_KEY,
    'number'     => $number,
];
if ($countryCode !== '') {
    $params['country_code'] = $countryCode;
}

$url = 'http://apilayer.net/api/validate?' . http_build_query($params);

$ch = curl_init($url);
curl_setopt_array($ch, [
    CURLOPT_RETURNTRANSFER => true,
    CURLOPT_TIMEOUT => 10,
]);
$response = curl_exec($ch);
$curlError = curl_error($ch);
curl_close($ch);

if ($response === false) {
    http_response_code(502);
    echo json_encode([
        'success' => false,
        'error' => ['info' => 'فشل الاتصال بـ Numverify: ' . $curlError]
    ]);
    exit;
}

echo $response;
