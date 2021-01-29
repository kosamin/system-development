
#### JWT操作（JWT->値取得）
```
$getUserInfoFromAzure = '';
if(isset($_POST['id_token'])) {
    $getUserInfoFromAzure = $_POST['id_token'];
}

$email = $this->getEmailFromJwt($getUserInfoFromAzure);

// JWTからweb会員情報のメールアドレスを取得する
public function getEmailFromJwt($id_token) {
    $id_token_array = explode('.', $id_token);
    $payload = base64_decode($id_token_array[1]);
    $payload_array = json_decode($payload, true);
    $email = $payload_array['emails'][0];
    return $email;
}
```
#### Azure ADB2Cのwebサインイン
- https://docs.microsoft.com/ja-jp/azure/active-directory-b2c/openid-connect

```
$redirect_url = $this->createRedirectUrl();

$tenantName = App_Config::getAppConfig()->resources->view->azure->params->tenantName;
$policyName = App_Config::getAppConfig()->resources->view->azure->params->policyName;
$clientId = App_Config::getAppConfig()->resources->view->azure->params->clientId;
$responseType ='id_token';
$scope = 'openid';
$responseMode ='form_post';
$nonce = $this->generateCode();

$urlEndpoint = "https://$tenantName.b2clogin.com/$tenantName.onmicrosoft.com/oauth2/v2.0/authorize?";
$urlParameters = "p=$policyName&client_id=$clientId&response_type=$responseType&scope=$scope&response_mode=$responseMode&redirect_uri=$redirect_url&nonce=$nonce";
$sendRequestToAzure = $urlEndpoint.$urlParameters;

// ランダム8桁数値を生成
public function generateCode($length = 8)
{
    $rand = rand(0, 99999999);
    $code = sprintf('%0'. $length . 'd', $rand);
    return $code;
}
```