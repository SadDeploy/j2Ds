# j2Ds - игровой HTML5 движок 

![j2Ds Demo](http://cs627318.vk.me/v627318778/14db8/1_gQygZQ2LY.jpg)

[Лицензия и описание](https://github.com/SkanerSoft/J2ds/wiki)

Автор движка: Skaner (Нагель Петр)

Главный помощник: Алексей

E-mail: skaner0@yandex.ru

vk: [vk.com/skaner.soft](http://vk.com/skaner.soft)

youtube: [youtube.com/skanersoft](http://youtube.com/skanersoft)

Сайты: [nagel-petr.ru](http://nagel-petr.ru), [skanersoft.ru](http://skanersoft.ru)



# Содержание:

1. [DOM](#dom)
1. [Математические операции](#math)
1. [Основы j2Ds](#j2ds)
1. [Ненужный include](#include)
1. [Локальное хранилище](#local)
1. [Размеры экрана и fullScreen()](#screen)
1. [Минимальный код](#minCode)
1. [Количество кадров в секунду (FPS)](#fps)
1. [Игровые состояния](#gameState)
1. [Сцена](#scene)
1. [Слои](#layers)
1. [Виды (ViewPorts)](#view)
1. [Устройство ввода](#input)
1. [Базовая нода](#addBaseNode)
1. [Прямоугольник](#addRectNode)
1. [Окружность](#addCircleNode)
1. [Линия](#addLineNode)
1. [Спрайт-карта и анимация](#createImageMap)
1. [Анимированные объекты](#addSpriteNode)
1. [Более сложный пример](#example)


## <a name="dom"></a> DOM
Для доступа к объектам по id:

    $id('id')

Для доступа по name:

    $name('name')

Для доступа по тегу:

    $tag('div')[x]

, где x - порядковый номер найденного элемента

## <a name="math"></a> Математические операции
В j2Ds все операции над координатми и размерами рекомендуется использовать посредством 
соответствующих классов или объектов.

Для задания целочисленных координат можно использовать объект:

    { x : 0, y : 0 }

или:

    j2ds.vector.vec2di(0, 0)

Для задания вещественных координат:

    { x : 0.0, y : 0.0 }

или:

    j2ds.vector.vec2df(0.0, 0.0)

Для округления до целого числа можно использовать:

    parseInt(14.5)

или:

    j2ds.math.toInt(14.5)

или:

    Math.ceil(14.5)

Для Рандомизации числа:

    var num= j2ds.math.random(0, 5);

Для Рандомизации к примеру от -15 до 15:

    var num= -15+Random(0, 30);

Для перевода градусов в радианы:

    var num= Rad(45); // 45'


> Примечание: для быстрого доступа к этим функциям можно создать ссылки на них:

    var vec2df = j2ds.vector.vec2df;
    var Random = j2ds.math.random;
    /* и др. */


## <a name="j2ds"></a> Глобальный j2ds

Любой объект в j2Ds - это нода определенного типа, наследующая те или иные 
свойства другой (или нескольких) ноды.

Структура построения дерева зависимости:

    root
     |-j2ds
       |-device
       |-scene
         |-layers
       |-input
       |-local

j2ds - Глобальный объект, дающий доступ к API движка.

В процессе выполнения игрового цикла вам доступны следующие переменные:

    j2ds.dt              // Фактор "Delta Time"
    j2ds.countDrawNodes  // Количество нод, отрисованных в текущий момент 

Для того, чтобы просмотреть структуру объекта, можно выполнить команду:

    console.log(j2ds);




## <a name="include"></a> Ненужный include

Для динамической подгрузки и выполнения JavaScript кода можно воспользоваться 
функцией:

    j2ds.include('my_script'); // выполнение файла my_script.js

если файл находится вне текущей папки, можно перед include присовить root 
каталог для поиска скрипта:

    j2ds.root= 'my_folder/';
    j2ds.include('my_script'); // выполнение my_folder/my_script.js

> Примечание: при множественном вызове одного и того же файла он выполнится
только при первом вызове. Остальные include для этого файла будут проигнорированы.


## <a name="local"></a> Локальное хранилище

Для хранения информации есть соответствующий объект:

    var myLocal = j2ds.createLocal('test');

, где 'test' - это уникальнй id для экземпляра хранилища. Уникальный инденификатор нужен 
для того, чтобы хранить идентичные поля.

Для сохранения информации:

    myLocal.save('myName', 'Skaner');

Для сохранения ноды/объекта:

    myLocal.saveNode('myNode', idOfNode);

, где idOfNode - объект или нода.

Для проверки существования переменной в хранилище:

    myLocal.is('myName'); // true/false

Для загрузки переменной:

    myLocal.load('myName'); // Skaner

Для загрузки объекта:

    myLocal.loadNode('myNode'); // Object "idOfNode"



## <a name="screen"></a> Device

Для определения размера экрана, доступного для развертывания игры, можно использовать объект:

    var device = j2ds.device();
    
    device.width     // ширина экрана
    device.height    // высота экрана

Чтобы развернуть игру на весь экран есть специальная команда:

    j2ds.scene.fullScreen(true);

> Примечание: команда работает только после инициализации игровой сцены командой *scene.init()*.


## <a name="minCode"></a> Минимальный игровой код

Чтобы подготовить страницу для организации игрового приложения, необходимо наличие обязательных элементов:

1. Скрипт движка
1. Холст (canvas) для отрисовки
1. Функция, описывающая игровое состояние

Код ниже представляет минимально необходимый код:
```html
<!DOCTYPE html>
<html> 
 <head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
  <script type="text/javascript" src="j2ds/j2ds.js"></script>
  <title>Пример игрового кода</title>
 </head>
 <body>

<canvas id="testCanvas" width="500" height="300"></canvas>

<script type="text/javascript">

// Объект сцены для быстрого доступа
var scene = j2ds.scene;

// вектор для быстрого доступа
var vec2di = j2ds.vector.vec2di;

// инициализация сцены
scene.init('testCanvas'); // id холста

// Игровое состояние Game
var Game = function () {
 // Очищаем сцену
 scene.clear(); 
 
 // Рисуем текст черным цветом в координатах 50 по x и 100 по y
 scene.drawText(vec2di(50, 100), 'Пример игрового приложения', 'black');
};

// После описания игрового состояния, оно не будет выполняться, пока игра не будет запущена.
// Для этого используется команда start:
scene.start(Game, 30); // Второй параметр - FPS

</script>

 </body>
</html>
```


## <a name="fps"></a> FPS

В игре вам часто может потребоваться ограничить Количество кадров в секунду, чтобы игра выполнялась
на одинаковой скорости при разных частотах процессора. Для этого необходимо при старте сцены
указать требуемое ограничение FPS:

    scene.start(Game, 30); // Старт игры с 30 fps

Кроме этого, вам так же доступен фактор Delta Time

    j2ds.dt // DeltaTime



## <a name="gameState"></a> Игровые состояния

В j2Ds для обработки игровой логики используется понятие "игрового состояния".
Игровое состояние - это функция, которая выполняется FPS количество раз.

Пример игрового состояния:

    var GameState = function() {
     
    };

Чтобы эта функция воспринималась движком, как игровое состояние, эту функцию нужно использовать
для старта игры, либо же установить в качестве игрового состояния функцией setEngine:
    
    // Игровое состояние 1
    var GameState1 = function() {
     if (j2ds.input.isKeyPress('SPACE')) {
      j2ds.scene.setEngine(GameState2);
     }
    };
    
    // Игровое состояние 2
    var GameState2 = function() {
     console.log('Активировано игровое состояние 2');
    };

    // Стартуем сцену с игровым состоянием 1
    j2ds.scene.start(GameState1, 25); // 25 fps



## <a name="scene"></a> Сцена

Сцена - основная нода игрового менеджера.

    // для облегчения доступа
    var scene = j2ds.scene;

### Инициализация
Чтобы инициализировать сцену и привязать ее к canvas-элементу, используется команда:

    scene.init('idCanvas');

### Старт
Чтобы начать игровой процесс, используется команда:

    scene.start(GameState, 25 [, userFunction]); // 25 fps

, где [, userFunction] - необязательый аргумент, являющийся функцией для выполнения в момент старта сцены.

Пример:

    scene.start(GameState, 25 function() {
     alert('Сцена стартовала на 25 FPS.');
    });

> Примечание: [, userFunction] выполнится лишь один раз за всю игру

### Смена игрового состояния
Для смены текущего игрового состояния на любое другое:

    scene.setEngine(GameState2); // мгновенно переключится на GameState2

> Примечание: GameState2 - это функция, описывающая игровое состояние.

### На весь экран
Чтобы развернуть сцену на всю доступную область экрана:

    // развернуть
    scene.fullScreen(true);
    // вернуть обратно
    scene.fullScreen(false);

### Очистка сцены
В игровом состоянии, если вы отрисовываете объекты, сцену нужно очищать перед
отрисовкой, чтобы не возник эффект "рисования":

    scene.clear();

### Заливка сцены
Если вам требуется залить сцену одним цветом, есть функция:

    scene.fill(color);

, где color - строка цвета, аналогичная цвету в CSS:

- 'black' - простое строчное именование цвета
- rgb(255, 255, 255) - цвет в формате rgb() или rgba()
- #A1F3C6

### Вывод текста
Вывод текста:

    var pos = vec2df(20, 20), // 20 на 20 пикселей
        text = 'Привет, мир!',
        color = 'red';
    
    scene.drawText(pos, text, color);

можно так же все указать прямо в функции:

    scene.drawText(vec2df(20, 20), 'Привет, мир!', 'red');

результат будет аналогичен.

Так же есть расширенная функция вывода текста:

    var pos = vec2df(20, 50),     // Позиция
        text = 'Привет, мир!',    // Текст
        font = 'bold 20px Arial', // Шрифт CSS
        color = 'green',          // Цвет текста
        colorLine = 'ywllow',     // Цвет обводки
        widthLine = 2;            // Толщина обводки

    scene.drawTextOpt(pos, text, font, color, colorLine, widthLine);

    
### Вывод изображения
Если требуется вывести на сцену изображение (не объект!), можете использовать команду:

    var pos = vec2df(10, 10),  // Позиция
        size = vec2df(50, 50), // Размер изображения
        frame = 1;             // Нужный кадр анимации

    scene.drawImage(pos, size, anim, frame);

> Примечание: об объекте 'anim' вы узнаете из раздела об изображениях



## <a name="layers"></a> Слои (Layers)

Для оптимизации производительности и снижения затрат на отрисовку статических объектов j2Ds
поддерживает возможность создания и обработки слоев.

    var scene = j2ds.scene;
    var layers = scene.layers;

### Сцена, как слой

Объект scene является центральным слоем, и новые слои можно добавлять как поверх него, так и позади.
Для определения глубины слоя используется индекс позиции:

    // Создание слоя с именем background и индексом позиции '-1',
    // Что означает, что слой будет располагаться позади основного слоя
    // на -1 уровень
    layers.add('background', -1);

Для того, чтобы расположить слой спереди, достаточно указать положительный индекс позиции.

### Доступ к слою

Для доступа к слою и его методам и свойствам используется команда:

    layers.layer('background').метод

### Заливка слоя

Для заливки слоя одним цветом:

    layers.layer('background').fill('blue');

Для заливки линейным градинетом:

    var colors = ['black', 'white', 'yellow'];

    layers.layer('background').fillGradientL(colors [, isHorizontal]);

, где isHorizontal - необязательный аргумент. Если true - завилвка будет горищонтальная


Для заливки радиальным градиентом:

    var pos1 = vec2df(15, 15),
        pos2 = vec2df(20, 20),
        radius1 = 20,
        radius2 = 60,
        colors = ['black', 'white'];
    layers.layer('background').fillGradientR(pos1, redius1, pos2, raduis2, colors);

### Скрыть/показать слой

Если слой необходимо скрыть или показать:

    layers.layer('background').setVisible(true/false);

### Прозрачность слоя

Для задания прозрачности слою есть команда:

    setAlpha(0.5);

Чтобы получить прозрачность:

    getAlpha()


### Перемещение слоя

Так же слой можно перемещать вперед и назад:

    var index = 1; // переместить слой спереди сцены на 1 уровень
    layers.layer('background').setIndex(index); 

### Методы рисования

Для соев так же актуальны следующие методы:

    layers.layer('background').drawText(pos, text, color);
                              .drawImage(pos, size, anim, frame);
                              .clear();
                              .clearNode(idNode);
                              .clearRect(pos, size);

### Удаление слоя

Если требуется удалить слой:

    layers.layer('background').destroy();



## <a name="view"></a> Виды (ViewPorts)

В j2Ds есть аналог камеры, которую можно двигать и фокусировать.

По умолчанию камера располагается в начале координат. Чтобы ее перемещать, используются
следующие команды.

### Фокусировка

    var scene = j2ds.scene;
    var pos = vec2df(600, 950);
    
    // сместить камеру на указанную позицию
    scene.setViewPosition(pos);


Так же камера может сфокусироваться на ноде:

    scene.setViewFocus(node, vec2df(offsetX, offsetY));

, где node - объект типа Node, а вторым параметром передается vec2df() для смещения.
Если второй параметр не указывать, камера сфокусируется так, что объект окажется в центре.

### Движение камеры

Так же вид камеры можно двигать:

    // если указать в игровом цикле, камера будет двигаться вправо
    scene.viewMove(vec2df(1, 0));




## <a name="input"></a> Устройства ввода

### Клавиатура

Для удобства работы с клавиатурой, тачскрином и мышью в j2Ds есть специальный объект:

    var input = j2ds.input;

Чтобы отслеживать нажатия клавиш на клавиатуре, есть специальные методы:

    var key = 'UP'; // клавиша - это обычная строка

    input.isKeyPress(key)
         .isKeyDown(key)
         .isKeyUp(key)

Все методы возаращают true/false в зависимости от состояни клавиши.

Пример:

    if (input.isKeyPress('SPASE')) {
     console.log('Нажатие клавиши "Пробел"');
    }

> Примечание: чтобы это сработало, проверки должны выполняться в пределах игровых состояний.

Так же, чтобы узнать сканкод клавиши, которая нажата в данный момент, существует специальная переменная:

    console.log(input.anyKey);

### Мышь

Для работы с мышью в объекте input существуют специальные свойства и методы:

Чтобы проверить, что нажата та или иная кнопка мыши:

    // Правая КМ
    input.rClick
    // Левая КМ
    input.lClick
    // Средняя КМ
    input.mClick

Пример:

    if (input.lClick) {
     console.log('Нажата левая кнопка мыши');
    }

*Курсор* мыши - это специальный объект, хранящий в себе координаты курсора мыши.

Чтобы узнать, где находится курсор мыши, можно использовать следующие команды:

    input.getPosition() - вернет объект типа vec2di с координатами X и Y.

аналогияно можно воспользоваться:

    input.pos - тот же vec2di

> Примечание: getPosition() и .pos возаращабт координаты с учетом смещения камеры.
Это предпочтительный способ получения координат мыши.

Однако он не подходит для определения координат курсора, проецирующихся на поверхность
игровой сцены. Для определения этих координат используется сам объект input:

    input.x,
    input.y

Пример:

    var scene = j2ds.scene;
    var input = j2ds.input;
    
    // Сдвинем камеру вправо на 100px
    scene.setViewPosition(vec2di(100, 0));
    
    // Вернет позицию курсора внутри игрового мира.
    // Если расположить курсор на 0,0 (самый левый угол),
    // то позиция курсора по X будет 100 или больше.
    console.log('Позиция курсора по X координате: ' + input.getPosition().x);
    
    // Вернет позицию курсора, проецирующуюся на сцену
    console.log('Позиция на canvas по X: ' + input.x);

Так же есть еще и абсолютная позиция курсора:

    input.abs - объект типа vec2di

### Тачскрин

Для определения касания, используются те же методы, что и для мыши:

    if (input.lClick) {
     scene.drawText(vec2df(10, 10), 'Касание!', 'red');
    }

Позиция прикосновения рассчитывается аналогично через input.pos, input,getPosition и input.



## <a name="addBaseNode"></a> Базовая нода

Базовая нода - это основной класс, который наследуется другими графическими объектами.
Объект базовой ноды имеет множество методов и свойств, присущий другим объектам типа Node,
и позволяет испольховать их.

Как таковой, объект BaseNode вам не пригодится, так как он не имеет функции отрисовки, однако все его 
методы наследованы другими объектами, и о них нужно знать.

### Создание

Чтобы создать объект BaseNode:

    var pos = vec2df(10, 10),  // позиция 
        size = vec2df(20, 20); // размер

    // Создание базовой ноды
    var a = scene.addBaseNode(pos, size);

Размер базовой ноде нужен для того, чтобы можно было корректно отображать Bounding-Box объектов.

### Основные свойства

Свойства объекта:

    // видимость объекта
    visible - true/false
    
    // Позиция
    pos - vec2df
    
    // Размер
    size - vec2df
    
    // Угол поворота (в градусах)
    angle
    
    // Слой для отрисовки
    layer

> Примечание: по умолчанию только созданный объект имеет слой для отрисовки - scene.

### Слои для отрисовки

Для переопределения слоя отрисовки используется команда:

    a.setLayer('background');

Чтобы узнать, какой слой используется объектом:

    var gettingLayer = a.getLayer();

### Collision-box и Bounding-box

По умолчанию все объекты имеют collision-box для определения пересечений, 
равный их bounding-box, но collision-box можно переопределить командой:

    offsetLeftTop = vec2df(5, 5); // смещение вниз и вправо
    offsetRightBottom = vec2df(-10, -10); // уменьшение размера
    a.resizeBox(offsetLeftTop, offsetRightBottom);

    /* где-то внутри игрового состояния : */
    a.drawBox();

### Скрыть/показать объект

Чтобы скрыть/отобразить объект:

    a.setVisible(true/false);

### Прозрачность

Чтоба задать прозрачность объекту, используется команда:

    a.setAlpha(0.5);

, где 0.5 - "сила" прозрачности. Аргумент принимает значения в диапазоне 0 - 1.

> Примачание: если объект полностью прозрачен (невидим), isLookScene() все равно вернет true, если он в пределах видимости сцены.

### Движение и позиция

Если необходимо двигать один объект в направлении другого, используется команда:

    var offset = vec2df(0, -10);
    a.moveTo(b, offset); // Объект a двигается к объекту b
, где offset - смещение. Если не указывать, объект а встанет точно в центр обхекта b.

Если требуется явно указать позицию объекта:

    var pos = vec2df(50, 900); // Позиция
    a.setPosition(pos);

или:

    a.setPosition(vec2df(50, 900));

или:

    a.pos.x= 50;
    a.pos.y= 90;

или:

    a.pos = vec2df(50, 900);

Чтобы двигать объект:

    a.move(vec2df(0, 1)); // Двигает объект вниз

или:


    a.pos.y+= 1;

или:

    a.pos= vec2df(0, a.pos.y+1);

или:

    a.setPosition(vec2df(0, a.getPosition().y+1));

или:

    a.move({x : 0, y : 1});

или:

    a.pos= {x : a.pos.x, y : a.pos.y + 1};


Чтобы получить текущую позицию объекта:

    var pos = a.getPosition();
    console.log(pos.x, pos.y);

или:

    var pos = a.pos;

### Размеры

Чтобы задать объекту нужный размер:

    a.setSize(vec2df(10, 10)); // Новый размер: 10x10 пикселей

### Расстояние между объектами

Чтобы получить расстояние между двумя объектами:

    var dist = a.getDistance(b);
    console.log(dist);

Чтобы получить дистанцию по осям, есть функция:

    var distX = a.getDistanceXY(b).x; // дистануия по оси X
    var distY = a.getDistanceXY(b).y; // по оси Y


### Пересечения

Чтобы проверить пересечения между двумя обхектами:

    a.isIntersect(b); // Вернет true, если объекты пересекаются

### Видимость в пределах сцены

Чтобы узнать, попадает ли объект в камеру, "видит ли его камера":

    a.isLookScene(); // если видим  true, иначе false

### Вращение

Если нужно вращать объект:

    a.turn(1); // вращает объект

> Примечание: можно в качестве параметра указывать любое число - это скорость вращения.
Если число положительное - по часовой стрелке, если отрицательное - против.


Чтобы задать угол вращения напрямую:

    a.setRotation(45); // повернуть объект на 45 градусов

Чтобы двигать объект в соответствии с его угом поворота:

    a.moveDir(speed);

, где speed - скорость перемещения


### Границы сцены

Так же может оказаться полезным проверка столкновения объекта с границами сцены:

    var isColl = a.isCollisionScene();
    
    console.log(isColl); // Object
    isColl.x; // Если < 0, то столкновение с левой границей, если > 0 - с правой
    isColl.y; // Если < 0, то столкновение с верхней границей, если > 0 - с нижней
    isColl.all; // Любой из вышеперечисленных


### Отображение bounding-box

Чтобы отобразить bounding-box и collision-box объекта:

    a.drawBox();

> Примечание: boundong-box рисуется черным цветом, collision-box - желтым.



## <a name="addRectNode"></a> Прямоугольник

Прямоугольник - это потомoк объекта [BaseNode](#addBaseNode), поэтому ему доступны все его свойства и методы.

Для создания прямоугольника:

    var pos = vec2df(0, 0),     // Позиция
        size = vec2df(25, 40);  // Размер
        color = 'green';        // Цвет

    // Создание поямругольной ноды
    var a = scene.addRectNode(pos, size, color);

Так как объект прямоугольник графический, кроме метода 'drawBox()' для рисования box-ов у него есть еще один метод:

    a.draw();

Он рисует созданный прямогугольник на соответствующий слой в соответстуйющей позиции.



## <a name="addCircleNode"></a> Окружность

Окружность - это потомoк объекта [BaseNode](#addBaseNode), поэтому ему доступны все его свойства и методы.

Для создания окружности:

    var pos = vec2df(0, 0),     // Позиция
        radius = 10;            // Радиус
        color = 'green';        // Цвет

    // Создание окружности
    var a = scene.addCircleNode(pos, radius, color);

Так как объект Окружность графический, кроме метода 'drawBox()' для рисования box-ов у него есть еще один метод:

    a.draw();

Он рисует созданную окружность на соответствующий слой в соответстуйющей позиции.




## <a name="addLineNode"></a> Линии

В j2Ds есть возможность создавать объекты из линий:

    var pos = vec2df(50, 50), // Позиция
        scale = 1,            // Фактор увеличения масштаба. > 1 увеличение, < 1 уменьшение
        width = 2,            // Толщина линий
        color = 'black',      // Цвет линии
        flagFill = true,      // Флаг (true/false) заливки объекта
        colorFill = 'red';    // Если указан флаг заливки, можно назначить цвет

    var points = [];          // Массив массивов точек

    points = [ [0, 0], [10, 10], [20, 0], [10, -10], [0, 0] ];

    var a = scene.addLineNode(pos, points, scale, color, width, flagFill, colorFill); 


Для отрисовки объекта так же используется методв draw():

    a.draw();



## <a name="createImageMap"></a> Спрайт-карта


В j2Ds есть возможность использовать спрайты, как объекты, взаимодействующие с игровым миром, но для их создания
требуется специально подготовленной изображение - спрайт-карта.
> Пример спрайт-карты: ![sptiteMap](http://spritedatabase.net/files/mobile/1356/Sprite/PS-OfficerNiao.png)

Формат такой спрайт-карты имеет только одно обязательное условия: *кадры должны располагаться горизонтально!*

Чтобы спрайт-карта была успешно создана и доступна для движка, она должна полностью загрузиться. По этой причине, чтобы загружать спрайт-карту
необходимо создать элемент IMG в DOM вашего документа:

    <img alt="" src="my_folder/my_image.png" id="myImageFromFile" />

И затем в нужном месте игрового приложения:

    var imageMap = scene.createImageMap('myImageFromFile');

Этой командой изображение исчезнет из видимой части DOM, но станет доступным для обработки внутри движка.

Но сам по себе объект imageMap ни с чем не может взаимодействовать, это просто подготовленный объект для взятия из него
необходимых спрайтов или анимаций. Чтобы "извлечь" анимацию или картинку из него, есть команда:

<a name="addSpriteNode-anim"></a>

    var sourceX = 0,    // Позиция первого кадра по X
        sourceY = 0,    // Позиция первого кадра по Y
        sourceH = 60,   // Высота первого кадра
        sourceW = 30;   // Ширина первого кадра
        frameCount = 1; // Количеество кадров, если это анимация
    
    // Создание анимации
    var anim = imageMap.createAnimation(sourceX, sourceY, sourceW, sourceH, frameCount);

> Примечание: Теперь такую анимацию можно передать в качестве аргумента для вывода на слой/сцену.





## <a name="addSpriteNode"></a> Анимированные объекты

Чтобы игра была насыщенней, в j2Ds есть возможность использовать не только прямоугольники и круги, вы так же можете
использовать изображения из спрайт-карт.

Спрайт - потомок [BaseNode](#addBaseNode), поэтому все его методы тут так же доступны.

Для создания спрайта существует команда:

    var pos = vec2df(10, 15);   // Позиция
        size = vec2df(30, 60);  // Размер

    var a = scene.addSpriteNode(pos, size, anim);

, где [anim](#addSpriteNode-anim) - анимация

Для отрисовки спрайта есть несколько методов:

    // Чтобы нарисовать любой один кадр анимации
    a.drawFrame(2); // Рисует второй кадр
    
    // Для отрисовки анимации целиком
    a.draw();
    
    // Если нужно замедлить скорость анимации, можно в метод draw() передать кол-во кадров, которые будут пропускаться
    a.draw(15); // При 30 fps анимация работает на скорость 30-15 FPS, то есть медленнее.

Если вы создали объект, и указали в качестве анимации 'anim1', и в процессе игры вам потребуется изменить анимацию
(прим.: уничтожение врага - взрыв), есть команда:

    a.setAnimation(amin2); // Присвоит анимацию anim2 спрайту.



## <a name="example"></a> Более сложный пример:

```html
<!DOCTYPE html>
<html> 
 <head>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />
  <script type="text/javascript" src="j2ds/j2ds.js"></script>
  <title>html5 page</title>
 </head>
 <body>

<img src="Demo/Base/j2Ds-Logo.png" id="jLogo" alt="">

<canvas id="testCanvas" width="900" height="300"></canvas>

<script type="text/javascript">

var dvc= j2ds.device();
var scene= j2ds.scene;
var input= j2ds.input;

var layers= scene.layers;

var vec2df= j2ds.vector.vec2df;
var vec2di= j2ds.vector.vec2di;
var Random= j2ds.math.random;

$id('testCanvas').width= dvc.width;
$id('testCanvas').height= dvc.height;

scene.init('testCanvas');

layers.add('back', -1);
layers.layer('back').fillGradientR(vec2df(scene.width/2, scene.height/4), 0,
                                   vec2df(scene.width/2, scene.height/2), scene.height*1.5,
                                   ['black', 'rgba(0,0,0,0)']);
                                   
layers.add('front', 1);
layers.layer('front').fillGradientL(['black', 'rgba(0,0,0,0)', 'black']);

layers.add('logo', 1);
//layers.layer('logo').setAlpha(0.4);

var jLogoMap= scene.createImageMap('jLogo');

var jAnim= jLogoMap.createAnimation(0, 0, 634, 314, 1);

var jLogo= scene.addSpriteNode(vec2df(0, 0), vec2df(634, 314), jAnim);
jLogo.setPosition(vec2df(scene.width/2, scene.height/2));
jLogo.setAlpha(0);
jLogo.setLayer('logo');

var max= 1000;

for (var i= 0, ob= []; i < max; i+=1) {
 var dy= Random(80, scene.height-80);
 var o= scene.addRectNode(vec2df(Random(0, scene.width-10), dy), vec2df(5, 5), '#E9E9E9');
 o.dx= Random(1, 5)*(dy*0.002);
 ob.push(o);
}

for (var i= 0, ob2= []; i < max; i+=1) {
 var dy= Random(80, scene.height-80);
 var o= scene.addRectNode(vec2df(Random(0, scene.width-10), dy), vec2df(5, 5), 'black');
 o.dx= -Random(1, 5)*(dy*0.002);
 ob2.push(o);
}

var Game= function () {
 
 scene.clear(); 
 
 for (var i= 0; i < max; i+=1) {

  if (ob2[i].isCollisionScene().x < 0) {
   ob2[i].pos.x= scene.width;
  }

  if (ob[i].isCollisionScene().x) {
   ob[i].pos.x= 0;
  }
  
  ob2[i].move(vec2df(ob2[i].dx*j2ds.dt, 0)); 
  ob[i].move(vec2df(ob[i].dx*j2ds.dt, 0));

  ob2[i].draw(); 
 }

 if (!input.lClick) {
  if (jLogo.alpha <= 1) {
   jLogo.setAlpha(jLogo.alpha+0.001*j2ds.dt);
  }
 } else {
  layers.layer('logo').clear();
  jLogo.setAlpha(0);  
 }

 jLogo.setPosition(vec2df(scene.width/2, scene.height/2));

 jLogo.draw();

 for (var i= 0; i < max; i+=1) {
  //  if (ob[i].isIntersect(jLogo)) {
  //   scene.context.shadowOffsetX = 5;
  //   scene.context.shadowOffsetY = 5;
  //   scene.context.shadowColor = "black";
  //   scene.context.shadowBlur = 2;
  //  } else {
  //   scene.context.shadowOffsetX = 0;
  //   scene.context.shadowOffsetY = 0;
  //   scene.context.shadowBlur = 0;
  //  }
  ob[i].draw();
 }

};

scene.start(Game, 60);

</script>


 </body>
</html>```

