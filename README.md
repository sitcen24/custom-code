# 1. Синхронизация скроллинга 2-х виджетов
```javascript
var histoUid = ''; // guid второго виджета (для синхронизации)
var bmrDetailDiv = $('#' + w.general.renderTo); // контейнер текущего виджета
var residentDetailDiv = $('#' + histoUid); // контейнер второго виджета
var scrollCf = 0.65; // коэфициент скроллинга,
                    // на случай разной высоты виджетов, scrollCf = 1, если виджеты равны
// добавление EventListener на событие скроллинга
document.getElementById(w.general.renderTo).addEventListener("scroll", function(){
    // пр скроллинге текущего виджета:
        // прокрутить текщий
        bmrDetailDiv.scrollTop($(this).scrollTop());
    
        // прокрутить второй на то же число пикселений * коэфициент скроллинга
        residentDetailDiv.scrollTop($(this).scrollTop() * scrollCf);
    
});
```

# 2. Кастомизация текстового виджета
## 2.1. Пресеты (готовые значения) на фильтры по датам
### 2.1.1 Checkbox (Даты)
На данный момент взаимодействие с куками отключено (некорректная работа)
```javascript
// Функция создания куки
function createCookie(name, value) {
    document.cookie = name + '=' + value + '; path=/';
}

// Функция чтения куки
function readCookie(name) {
    var nameEQ = name + '=',
    allCookies = document.cookie.split(';'),
    i,
    cookie;
    for (i = 0; i < allCookies.length; i += 1) {
      cookie = allCookies[i];
      while (cookie.charAt(0) === ' ') {
        cookie = cookie.substring(1, cookie.length);
      }
      if (cookie.indexOf(nameEQ) === 0) {
        return cookie.substring(nameEQ.length, cookie.length);
      }
    }
    return null;
  }

// GUID фильтра, который нужно выставлять
var filterGuid = "";

// Значения фиьтра для пресета
var dates = [
                new Date(2021, 0, 1, 0, 0, 0), // Дата начала фильтра
                new Date() // Дата конца фильтра (сегодня)
        ];

// Значения фильтра для значения по умолчанию (при снятии галочки с чекбокса)
var dateNow = new Date();
dateNow.setDate(dateNow.getDate() - 1);
var defDates = [
                new Date(2022, 0, 1, 0, 0, 0), // Дата начала фильтра
                dateNow // Дата конца фильтра
        ];

// Переменная под предыдущие значения
var oldVals;

// Вместо текста создаем checkbox
w.general.text = "<span>   \
                        <input type='checkbox' id='datePreset-" + w.general.renderTo + "' class='checkboxPreset-" + w.general.renderTo + "'> \
                            <label for='datePreset-" + w.general.renderTo + "'> \
                                <span style='font-weight: 550'>" + w.general.text + "</span>   \
                            </label>   \
                    </span>  \
                  ";
                  
// Рендер виджета   
TextRender({
    text: w.general,
    style: w.style
});

// Добавление обработчика изменения чекбокса
document.getElementById('datePreset-' + w.general.renderTo).addEventListener("change", function(){
    
    // Если выставили чекбокс
    if(this.checked){
        // Собираем старые значения фильтра
        oldVals = visApi().getSelectedValues(filterGuid);
        // Кладем их в куки
        // createCookie('oldVal1', oldVals[0][0].toJSON());
        // createCookie('oldVal2', oldVals[0][1].toJSON());
        // Кладем в фильтр новые значения
        visApi().setDateFilterSelectedValues(filterGuid, dates);
    }
    // Если чекбокс отключили
    else{
        // Собираем старые значения фильтров из кук
        // oldVals = [
        //             new Date(readCookie('oldVal1')),
        //             new Date(readCookie('oldVal2'))
        //         ];
        // Выставляем в фильтр старые значения
        visApi().setDateFilterSelectedValues(filterGuid, defDates);
    }
});

// Визуальная настройка чекбокса
$('.checkboxPreset-' + w.general.renderTo).css({
	'margin-left': '15px',
	'margin-top': '15px',
	'margin-right': '10px',
	'border-radius': '3px',
	'transform': 'scale(1.5)'
});
```
### 2.1.2 Radio Button (Даты)
На данный момент взаимодействие с куками отключено (некорректная работа)
```javascript
// Символ для разделения значений из текста
var splitSymb = '#';
// Символ значения по умолчанию
var checkedSymb = '$';
/*Работает следующим образом:
    Запись в поле "Текст" текстового виджета вида "$Сутки#Неделя#Месяц"
    Будет означать, что существует 3 кнопки: 'Сутки', 'Неделя' и 'Месяц'    
        где значение по умолчанию -- 'Сутки'
*/

// GUID фильтра, который нужно выставлять
var filterGuid = "";

// Значения фильтра для пресета
var dateNow = new Date();
var d0 = new Date()
var d1 = new Date()
var d2 = new Date()
d0.setDate(dateNow.getDate() - 1);
d1.setDate(dateNow.getDate() - 8);
d2.setDate(dateNow.getDate() - 30);

// Само значение -- структуры [ ['Дата от 1', 'Дата до 1'], ['Дата от 2', 'Дата до 2'], ...]
var dates = [
                [ // для 0 варианта
                    d0, // Дата начала фильтра
                    d0 // Дата конца фильтра
                ],
                [ // для 1 варианта
                    d1, // Дата начала фильтра
                    d0 // Дата конца фильтра
                ],
                [ // для 2 варианта
                    d2, // Дата начала фильтра
                    d0 // Дата конца фильтра
                ]
        ];

// Переменная под предыдущие значения
var oldVals;

function makeRadios(values){
    var template = "<span>";
    for(var i = 0; i < values.length; i++){
        var ch = ''
        if(values[i][0] == checkedSymb){
            ch = 'checked';
            values[i] = values[i].slice(1);
        }
        template = template + "<input type='radio' id='datePreset" + i + "-" + w.general.renderTo + "' class='radioPreset-" + w.general.renderTo + "' name='rad1-" + w.general.renderTo + "' " + ch + "> \
                            <label for='datePreset" + i + "-" + w.general.renderTo + "'> \
                                <span style='font-weight: 550'>" + values[i] + "</span>   \
                            </label>"
    }
    template = template + "</span>"
    return template
}

function addListeners(values){
    for(var i = 0; i < values.length; i++){
        
    }
}

// Вместо текста создаем radio
w.general.text = makeRadios(w.general.text.split(splitSymb));

// Рендер виджета   
TextRender({
    text: w.general,
    style: w.style
});

// Добавление обработчиков изменения радио (необходимо проставить руками для каждого значения)
document.getElementById('datePreset0-' + w.general.renderTo).addEventListener("change", function(){
    // Если выставили значение
    if(this.checked){
        visApi().setDateFilterSelectedValues(filterGuid, dates[0]);
    }
});
document.getElementById('datePreset1-' + w.general.renderTo).addEventListener("change", function(){
    // Если выставили значение
    if(this.checked){
        visApi().setDateFilterSelectedValues(filterGuid, dates[1]);
    }
});
document.getElementById('datePreset2-' + w.general.renderTo).addEventListener("change", function(){
    // Если выставили значение
    if(this.checked){
        visApi().setDateFilterSelectedValues(filterGuid, dates[2]);
    }
});
// Визуальная настройка радио
$('.radioPreset-' + w.general.renderTo).css({
    'margin-top': '15px',
	'transform': 'scale(1.3)'
});
```
