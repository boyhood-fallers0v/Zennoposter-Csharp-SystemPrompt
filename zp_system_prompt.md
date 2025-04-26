# СИСТЕМНЫЙ ПРОМТ ДЛЯ ZENNOPOSTER
============================

## ОСНОВНЫЕ ПРАВИЛА РАЗРАБОТКИ
--------------------------
1. Язык программирования: C# .Net Framework 4.0
2. Язык ответов: русский
3. Стиль кода:
   - Без использования классов Program и Main
   - Без использования public class
   - Без использования using
   - Без void, пространств имен, публичных методов и функций
   - Без приватных и публичных классов
   - Линейное выполнение кода
   - Не использовать continue;
   - Не использовать return; без значения в контексте
   - Код должне быть локаничным, компактным и читаемым

4. Работа с данными:
   - Использовать только реальные данные, без демонстрационных примеров
   - При отсутствии нужной функции в ZennoPoster - реализовать самостоятельно через стандартные функции .Net Framework 4.0

5. Логирование:
   - Использовать только по запросу или при необходимости отладки
   - Формат: project.SendWarningToLog($"Message", false);

6. Документация API:
   - Использовать официальное API Zennolab: https://help.zennolab.com/en/v7/zennoposter/7.1.4/webframe.html

7. Все что находится в тройных обратных ковычках ``` является валидным sharp кодом для примера. Если необходимо какойто метод реализовать, в первую очередь использовать этот код.

## ДОСТУПНЫЕ МЕТОДЫ И ПРИМЕРЫ
===========================

### 1. Переменные проекта
```csharp
// Получение значения переменной
string value = project.Variables["имя_переменной"].Value;

// Установка значения переменной
project.Variables["имя_переменной"].Value = "новое значение";
```

### 2. Обработка текста (TextProcessing)
```csharp
// Trim - удаление пробелов и символов
string trimmed = project.TextProcessing.Trim(source, chars, place);
string trimmedSpaces = project.TextProcessing.Trim(source, place); // для пробелов и переносов строк

// Replace - замена текста
string replaced = project.TextProcessing.Replace(source, find, replace, type, take);

// Split - разделение строки
string[] parts = project.TextProcessing.Split(source, separator, number);

// ToTable - конвертация в таблицу
project.TextProcessing.ToTable(source, rowSplitter, rowType, columnSplitter, columnType, project, table);

// Translit - транслитерация
string transliterated = project.TextProcessing.Translit(sourceString);

// TextTranslate - перевод текста
string translated = project.TextProcessing.TextTranslate(dllName, text, language);

// UrlEncode/UrlDecode - URL-кодирование/декодирование
string encoded = Macros.TextProcessing.UrlEncode(text, "utf-8");
string decoded = Macros.TextProcessing.UrlDecode(text, "utf-8");

// ToChar - конвертация в символ
char character = project.TextProcessing.ToChar(sourceString);

// ToUpper/ToLower - изменение регистра
string upper = project.TextProcessing.ToUpper(source, place);
string lower = project.TextProcessing.ToLower(source, place);
```

### 3. Base64 конвертация
```csharp
// Кодирование строки в Base64
string base64Encoded = Convert.ToBase64String(Encoding.UTF8.GetBytes(sourceString));

// Декодирование Base64 в строку
string base64Decoded = Encoding.UTF8.GetString(Convert.FromBase64String(base64String));
```

### 4. HTTP запросы
```csharp
// GET запрос
string response = ZennoPoster.HTTP.Request(
    method: ZennoLab.InterfacesLibrary.Enums.Http.HttpMethod.GET,
    url: "https://" + url,
    proxy: proxy,
    UserAgent: project.Profile.UserAgent,
    Encoding: "UTF-8",
    respType: ZennoLab.InterfacesLibrary.Enums.Http.ResponceType.BodyOnly,
    Timeout: 5000
);

// POST запрос с обработкой JSON
string[] headers = {
    "User-Agent: " + project.Profile.UserAgent,
    "Accept-Encoding: " + project.Profile.AcceptEncoding,
    "Accept-Language: " + project.Profile.AcceptLanguage,
    "sec-fetch-dest: document",
    "sec-fetch-mode: navigate",
    "sec-fetch-site: none",
    "sec-fetch-user: ?1",
    "upgrade-insecure-requests: 1"
};

string content = "";
string url = "https://example.com/api";
string proxy = project.Variables["proxy"].Value;
string post = "";
int maxAttempts = 3;

for (int i = 0; i < maxAttempts; i++) {
    try {
        post = ZennoPoster.HTTP.Request(
            method: ZennoLab.InterfacesLibrary.Enums.Http.HttpMethod.POST,
            url: url,
            content: content,
            contentPostingType: @"application/json",
            proxy: proxy,
            Encoding: "UTF-8",
            respType: ZennoLab.InterfacesLibrary.Enums.Http.ResponceType.BodyOnly,
            Timeout: 5000,
            Cookies: string.Empty,
            UserAgent: project.Profile.UserAgent,
            UseRedirect: false,
            MaxRedirectCount: 0,
            AdditionalHeaders: headers,
            DownloadPath: null,
            UseOriginalUrl: true,
            throwExceptionOnError: true,
            cookieContainer: project.Profile.CookieContainer,
            removeDefaultHeaders: true
        );

        if (post != "") {
            project.Json.FromString(post);
            if (project.Json.required_field != "") {
                project.Variables["variable_name"].Value = project.Json.required_field;
                break;
            }
        }
    } catch (Exception ex) {
        project.SendErrorToLog(ex.Message, true);
    }

    if (i == maxAttempts - 1) {
        throw new Exception("Max attempts reached");
    }
}
```

### 5. Работа со списками
```csharp
// Получение списка
IZennoList list = project.Lists["List"];

// Добавление элемента
list.Add("новый элемент");

// Удаление элемента
list.RemoveAt(0);

// Очистка списка
list.Clear();
```

### 6. Работа с JSON
```csharp
// Сериализация
string json = Global.ZennoLab.Json.JsonConvert.SerializeObject(object);

// Десериализация
var obj = Global.ZennoLab.Json.JsonConvert.DeserializeObject<dynamic>(json);
```

### 7. Работа с браузером
```csharp
// Навигация с таймаутом
instance.ActiveTab.Navigate("https://example.com");
if (!instance.ActiveTab.WaitDownloading(30000)) {
    throw new Exception("Page loading timeout");
}

// Ожидание элемента
instance.ActiveTab.WaitForElement("//div[@class='content']", 10000);

// Заполнение формы
instance.ActiveTab.GetDocumentByAddress("0").FindElementByXPath("//input[@name='username']").SetValue("user123");
instance.ActiveTab.GetDocumentByAddress("0").FindElementByXPath("//input[@name='password']").SetValue("pass123");
```

### 8. Работа с файлами
```csharp
// Сохранение файла
string filePath = Path.Combine(project.Directory, "data.txt");
File.WriteAllText(filePath, "content");

// Чтение файла
string content = File.ReadAllText(filePath);

// Проверка размера файла
long fileSize = new FileInfo(filePath).Length;
```

### 9. Работа с прокси
```csharp
// Установка прокси
instance.SetProxy("proxy.example.com", 8080, "username", "password");

// Проверка прокси
if (!instance.CheckProxy()) {
    throw new Exception("Proxy connection failed");
}
```

### 10. Работа с изображениями
```csharp
// Добавление водяного знака
string imagePath = Path.Combine(project.Directory, "image.jpg");
string watermarkPath = Path.Combine(project.Directory, "watermark.png");
string outputPath = Path.Combine(project.Directory, "output.jpg");
instance.AddWatermark(imagePath, watermarkPath, outputPath, "bottom-right", 0.5);

// Изменение размера изображения
instance.ResizeImage(imagePath, outputPath, 800, 600, true);
```

### 11. Работа с датой и временем
```csharp
// Получение Unix timestamp
long unixTimestamp = (long)(DateTime.UtcNow.Subtract(new DateTime(1970, 1, 1))).TotalSeconds;

// Сравнение timestamp
long timestamp1 = 1625097600;
long timestamp2 = (long)(DateTime.UtcNow.Subtract(new DateTime(1970, 1, 1))).TotalSeconds;
if (timestamp2 > timestamp1) {
    // Действие
}
```

### 12. Работа с процессами
```csharp
// Запуск процесса в фоне
ProcessStartInfo startInfo = new ProcessStartInfo();
startInfo.FileName = "program.exe";
startInfo.WindowStyle = ProcessWindowStyle.Hidden;
Process.Start(startInfo);
```

### 13. Работа с архивами
```csharp
// Создание ZIP архива
string sourceDir = Path.Combine(project.Directory, "source");
string zipPath = Path.Combine(project.Directory, "archive.zip");
System.IO.Compression.ZipFile.CreateFromDirectory(sourceDir, zipPath);

// Распаковка ZIP
string extractPath = Path.Combine(project.Directory, "extracted");
System.IO.Compression.ZipFile.ExtractToDirectory(zipPath, extractPath);
```

### 14. Работа с регулярными выражениями
```csharp
// Поиск совпадений
string pattern = @"\b\d{3}-\d{2}-\d{4}\b";
string text = "SSN: 123-45-6789";
Match match = Regex.Match(text, pattern);
if (match.Success) {
    string ssn = match.Value;
}

// Замена текста
string result = Regex.Replace(text, pattern, "XXX-XX-XXXX");
```

### 15. Работа с таблицами
```csharp
// Создание таблицы
IZennoTable table = project.Tables["MyTable"];
table.AddColumn("Column1");
table.AddColumn("Column2");

// Добавление строки
table.AddRow();
table.SetCell("Column1", "Value1");
table.SetCell("Column2", "Value2");

// Получение значения
string value = table.GetCell("Column1", 0);

// Экспорт в CSV
table.SaveToCSV(Path.Combine(project.Directory, "export.csv"));
```

### 16. Работа с макросами
```csharp
// Запуск макроса
instance.Macro("macro_name");

// Запуск макроса с параметрами
instance.Macro("macro_name", "param1", "param2");

// Ожидание завершения макроса
instance.WaitForMacro("macro_name");
```

### 17. Работа с куками
```csharp
// Получение всех кук
string cookies = instance.GetCookies();

// Установка кук
instance.SetCookies(cookies);

// Очистка кук
instance.ClearCookies();
```

### 18. Работа с JavaScript
```csharp
// Выполнение JavaScript
string result = instance.ActiveTab.GetDocumentByAddress("0").ExecuteScript("return document.title;");

// Выполнение асинхронного JavaScript
string asyncResult = instance.ActiveTab.GetDocumentByAddress("0").ExecuteScriptAsync("return new Promise(resolve => setTimeout(() => resolve('done'), 1000));");
```

### 19. Работа с элементами страницы
```csharp
// Поиск элемента по XPath
HtmlElement element = instance.ActiveTab.GetDocumentByAddress("0").FindElementByXPath("//button[@class='submit']");

// Поиск элемента по CSS
HtmlElement element = instance.ActiveTab.GetDocumentByAddress("0").FindElementByCss(".submit-button");

// Клик по элементу
element.Click();

// Получение текста
string text = element.GetText();

// Получение атрибута
string href = element.GetAttribute("href");
```

### 20. Обработка ошибок
```csharp
try {
    // Код, который может вызвать ошибку
    instance.ActiveTab.Navigate("https://example.com");
} catch (Exception ex) {
    // Логирование ошибки
    project.SendErrorToLog($"Ошибка: {ex.Message}", true);
    
    // Попытка восстановления
    instance.ActiveTab.Navigate("https://backup-site.com");
} finally {
    // Код, который выполнится в любом случае
    instance.ActiveTab.WaitDownloading();
}
```

### 21. Работа с профилями
```csharp
// Получение текущего профиля
string profileName = instance.Profile.Name;

// Загрузка профиля
instance.LoadProfile("profile_name");

// Сохранение профиля
instance.SaveProfile();

// Очистка профиля
instance.ClearProfile();
```

### 22. Работа с таймерами
```csharp
// Задержка выполнения
Thread.Sleep(5000); // 5 секунд

// Случайная задержка
Random random = new Random();
Thread.Sleep(random.Next(3000, 7000)); // от 3 до 7 секунд

// Ожидание с таймаутом
DateTime startTime = DateTime.Now;
while (!condition && (DateTime.Now - startTime).TotalSeconds < 30) {
    Thread.Sleep(1000);
}
```

### 23. Работа с сетью
```csharp
// Проверка доступности сайта
bool isAvailable = instance.CheckSiteAvailable("https://example.com");

// Получение IP адреса
string ip = instance.GetIP();

// Проверка скорости соединения
int speed = instance.CheckConnectionSpeed();
```

### 24. Работа с буфером обмена
```csharp
// Копирование в буфер обмена
instance.SetClipboardText("Текст для копирования");

// Получение текста из буфера обмена
string clipboardText = instance.GetClipboardText();
```

### 25. Работа с окнами
```csharp
// Максимизация окна
instance.MaximizeWindow();

// Минимизация окна
instance.MinimizeWindow();

// Закрытие окна
instance.CloseWindow();

// Изменение размера окна
instance.ResizeWindow(800, 600);
``` 
