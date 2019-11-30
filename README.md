# Как добавить иконку

Типичная ситуация: у вас есть элемент, а рядом с ним - иконка, как, например, здесь (макет "[Cat Energy](https://htmlacademy.ru/intensive/adaptive)"):

![Заголовок карточки](/img/panther-block.png)

Иконку можно экспортировать из макета в таком виде:

![Иконка "Пантера"](/img/panther.png)

Но как добавить её на страницу? Очевидно, что это декоративный элемент, и мы не будем добавлять его в разметку. Добавим его псевдоэлементом. Часто встречаю такую реализацию: под иконку создаётся контейнер, иконка подкладывается фоном, выравнивается внутри него, контейнер абсолютом выравнивается в левом паддинге исходного элемента. Такой css может выглядеть примерно так:

##### Не самая удачная реализация

```css
.mass-gain {
    position: relative;
    padding-left: 165px;

    font-family: 'Oswald', sans-serif;
    font-weight: 400;
    font-size: 36px;
    line-height: 100px;
}

.mass-gain::before {
    content: '';
    position: absolute;
    left: 17px;
    top: 0;

    width: 100px;
    height: 100px;

    background-color: #68b738;
    border-radius: 50%;
}

.mass-gain::after {
    content: '';
    position: absolute;
    left: 0;
    top: 22px;

    width: 134px;
    height: 56px;

    background-image: url('img/panther.png');
    background-repeat: no-repeat;
}
```

**Результат [тут](bad.html)**

Это нормальное решение, оно работает, но посмотрите, сколько кода мы написали, а задача вроде стояла простая - добавить одну иконку слева от блока текста. К тому же такое решение довольно чувствительно к размеру иконки, то есть такой css плохо переиспользуем. Можно ли проще? Да, можно. Мы могли бы экспортировать иконку сразу с зелёным кружком, несмотря на то, что умеем рисовать его на css - это избавило бы нас от блока кода `::after {}`, но мы всё равно остались в рамках текущей реализации: `absolute + background + padding`.
Можно ли ещё проще? Можно.

##### Реализация лучше

```css
.mass-gain {
    display: flex;
    align-items: center;

    font-family: 'Oswald', sans-serif;
    font-weight: 400;
    font-size: 36px;
}

.mass-gain::before {
    content: url('img/panther.png');

    display: flex;
    justify-content: center;
    align-items: center;

    margin-right: 65px;
    width: 100px;
    height: 100px;

    background-color: #68b738;
    border-radius: 50%;
}
```

Никаких абсолютов и прочих зыбких решений - только гибкие флексбоксы. Иконка добавляется через свойство `content`, правым маргином отталкивает от себя текстовый блок, что очень важно - находится в потоке. Просто добавив селектору `.mass-gain` свойство `flex-direction: column;`, мы можем поместить иконку над текстовым блоком. Подумайте, сколько свойств нам пришлось бы переопределить для реализации [выше](#Не-самая-удачная-реализация)?

**Результат [тут](better.html)**

А если экспортировать иконку с зелёным кружком, наш код сокращается ещё существеннее.

##### Вполне достойная реализация

```css
.mass-gain {
    display: flex;
    align-items: center;

    font-family: 'Oswald', sans-serif;
    font-weight: 400;
    font-size: 36px;
}

.mass-gain::before {
    content: url('img/panther-with-circle.png');
    margin-right: 65px;
}
```

**Результат [тут](good.html)**

Последние два решения кроссбраузерные, простые и очевидные. Добавляемая иконка остаётся в потоке, это избавляет нас от многих потенциальных проблем с перекрытием иконки другими элементами страницы, например, при переполнении. А флексовый контейнер позволяет лёгким способом менять её положение, что может быть полезно при создании адаптивных интерфейсов.

Отдельно стоит сказать про иконки-символы, как, например, здесь:

![Ссылка на каталог](/img/arrow-block.png)

Может показаться, что иконку-стрелку нужно экспортировать из макета (непременно в svg-формате) и добавить на страницу одним из описанных выше способов. На самом деле нет ничего проще, чем добавить её символом юникода. Обычно я пользуюсь сайтом [unicode-table.com](https://unicode-table.com/ru/), вбиваю в поиске, что хочу найти, например, "[стрелка вправо](https://unicode-table.com/ru/search/?q=%D1%81%D1%82%D1%80%D0%B5%D0%BB%D0%BA%D0%B0+%D0%B2%D0%BF%D1%80%D0%B0%D0%B2%D0%BE)", в результатах нахожу нужный мне символ и копирую. Мне кажется, "Стрелка направо с треугольным остриём" - то, что нужно. Я мог бы добавить её в css таким образом:

```css
.link {
    display: flex;
    align-items: center;
    
    font-family: 'Oswald', sans-serif;
    font-size: 20px;
    color: #000000;
    text-decoration: none;
}

.link::after {
    content: '⭢';
    margin-left: 20px;
    font-size: 30px;
    line-height: 1;
    padding-bottom: 3px;
}
```

А мог бы добавить в html и стилизовать:

```html
<a href="" class="link">КАТАЛОГ SLIM <span class="link__arrow">⭢</span></a>
```

```css
.link {
    display: flex;
    align-items: center;

    font-family: 'Oswald', sans-serif;
    font-size: 20px;
    color: #000000;
    text-decoration: none;
}

.link__arrow {
    margin-left: 20px;
    font-size: 30px;
    line-height: 1;
    padding-bottom: 3px;
}
```

Разница только в том, что во втором случае стрелка будет присутствовать на странице в явном виде, даже если по какой-то причине не сработают стили.
