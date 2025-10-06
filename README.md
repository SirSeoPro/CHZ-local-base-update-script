# PowerShell скрипт инициализации локальной БД ЧЗ по списку
1) Создаём рядом со скриптом файл "POS.txt", наполняем его списком касс. Каждая строка - новое имя.
2) Запускаем скрипт:
```
$authToken = "Пароль_локальной_бд"
$bodyToken = "Токен_ЧЗ"
$computers = Get-Content -Path "POS.txt"

$headers = @{
    "Content-Type"  = "application/json"
    "Authorization" = "Basic $authToken"
}

$body = @{
    token = $bodyToken
} | ConvertTo-Json -Compress

foreach ($line in $computers) {
    $computerName = $line.Trim()
    Write-Host "Инициализация POS: [$computerName] (длина: $($computerName.Length))"

    $url = "http://$($computerName):5995/api/v1/init"
    Write-Host "URL запроса: $url"

    try {
        $response = Invoke-RestMethod -Uri $url -Headers $headers -Method POST -Body $body -TimeoutSec 10
        Write-Host "POS $computerName - ответ: $($response)"
    } catch {
        Write-Host "POS $computerName недоступен или ошибка: $($_.Exception.Message)" -ForegroundColor Red
    }
    Write-Host "-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-=-"
}
```
