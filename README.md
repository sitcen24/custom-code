# 1. Синхронизация скроллинга 2-х виджетов

```html
var histoUid = ''; // guid второго виджета (для синхронизации)
var bmrDetailDiv = $('#' + w.general.renderTo); // контейнер текущего виджета
var residentDetailDiv = $('#' + histoUid); // контейнер второго виджета
var scrollCf = 0.65; // коэфициент скроллинга,
                    // на случай разной высоты виджетов, scrollCf = 1, если виджеты равны
// добавление EventListener на событие скроллинга
document.getElementById('grid-' + w.general.renderTo).addEventListener("scroll", function(){
    // пр скроллинге текущего виджета:
        // прокрутить текщий
        bmrDetailDiv.scrollTop($(this).scrollTop());

        // прокрутить второй на то же число пикселений * коэфициент скроллинга
        residentDetailDiv.scrollTop($(this).scrollTop() * scrollCf);

});

```
