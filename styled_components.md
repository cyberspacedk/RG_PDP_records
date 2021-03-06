![pic](http://i.piccy.info/i9/ec5c91dae17c0a0970b9e708b5255c8e/1556390885/40738/1314369/1_6E_EG6HczqSSsEQFgFlG_A.jpg)

-  [Intro](#Intro)  
 
- [Стилизация](#Стилизация)  
  - [Используем переменные](#Using_variables)  
  - [Props](#Props)   
  - [Наследование стилей](#Inheritans)  
  - [Стили для вложенных елементов](#Style_for_inside_element)  
  - [Использование символа &](#&)
  - [Медиа правила](#Media_Queries)   
  - [Хелперы / Миксины](#Helper_Mixins)   
  - [Animations](#Animations)  
  - [Глобальные стили](#Global_Styles)  

- [Темы](#Themes)

- [Атрибуты](#Атрибуты)
  - [attrs](#attrs)   
  - [as](#as)
  - [css](#css) 

- [HOC](#HOС)
  - [styled](#styled)  
  - [withTheme](#withTheme)

- [Under the hood](#Under_the_hood)  

### Intro

`Styled components` еще один способ написания CSS in JS.  

[npm trends](https://www.npmtrends.com/css-in-js-vs-css-in-js-loader-vs-styled-components-vs-glamorous-vs-aphrodite-vs-emotion-vs-jss)


Pешают такие задачи как:

📌 Критический CSS  
Получаем стилизованную страницу сразу без задержек. `styled-components` генерирует стили только для тех компонентов, которые попадают в рендер, + к производительности + UI. 

📌 Модульность / Нет кофликтов имен классов  
`styled-components` генерирует **уникальные имена** классов для ваших стилей.  

📌 Нет мертвого css кода   
При удалении компоненты удаляется также связанные с ним стили, при традиционной стилизации все css правила существуют пока не будут удалены физически

📌 Динамическая стилизация  
Стили компоненты изменяются в зависимости от переданных `props` или `theme`. Код немного чище, так как не приходится писать условия в `className` или в потоке кода.
  
📌 Автоматическое проставление вендорных префиксов  и минификация css 

📌 Нет необходимости создавать дополнительные файлы стилей `.css`

Использует css-препроцессор  [stylis](https://github.com/thysultan/stylis.js)


## Стилизация 

Styled Components использует шаблонные строки (template literals) для описания css правил. 

### Simple_style

На объекте `styled` создаем новое поле (html элемент) который появится в разметке

В `temlate literal` пишем привычный css

```jsx
const Button = styled.button`
  padding: 25px;
  background-color: palevioletred;
  border: none;
  margin: 20px;
`;
```

### Using_variables

Использование переменных аналогично вставке JS выражений в TL.  `${...}` 

```jsx
let size = 12;
let colorRegular = "#ccc";

const Button = styled.button`
  padding: 25px;
  margin-bottom: ${size}px;
  background-color: palevioletred;
  border: none;
  margin: 20px;
  color: ${colorRegular};
`;
```

### Props

В CSS свойство, которое будет зависеть от переданного `props` передаем callback функцию, которая в агрументах получит ссылку на объект `props` компоненты и в зависимости от значений `props` возвращаем из ф-ции необходимый `css`

```jsx
const Button = styled.button` 
  background: ${props => props.primary ? "palevioletred" : "white"};
  color: ${props => props.primary ? "white" : "palevioletred"}; 
  border: 2px solid palevioletred;
  border-radius: 3px;
`;

```

### Inheritans

Если необходимо наследовать стили от другой компоненты, то следует передать этот компонент в метод `styled(...)`, а в TL указываем переопределяющие CSS правила

```jsx
// Наследуемся от компоненты Button и переопределям только цвет шрифта
const Cancel = styled(Button)`
  color: "#fff";
`;
```

### Style_for_children

Чтобы не создавать новый `styled` компонент, можно в родительском css styled блоке писать правила для вложенных елементов, по селектору (класса, тега и т.п)

```jsx
// Родительский блок
const Appheader = styled.header`
  padding: 10px;
  text-align: center;
  // правила для дочернего елемента с классом .color
  .color {
    background-color: yellow;
    border: 2px solid #000;
    font-weight: 800;
  }
`;
 
<Appheader>
  <span className="color">App header</span>
</Appheader>;
```
 
### Media_Queries

Стандартное использование директивы `@media`

```jsx 
const Controls = styled.div`
  button {
    background-color: palevioletred;
    &:hover {
      background-color: green;
    }
  }
// Начиная с минимальной ширины сработают правила
  @media (min-width: 768px) {
    background-color: purple;
// также можно выбирать отдельно для компоненты
    ${Button} {
      color: green;
    }
  }
`;

```

### Helper_Mixins

Можно объявить переменную миксин/хелпер и использовать ее в стилях компоненты.

- если хелпер это просто набор css правил то используем стандартное описание 

```jsx
const font = `
    font-family: Monospace;
    font-size: 20px;
    color: red
`; 

const Title = styled.h1`
  width: 10 rem;
  margin-bottom: 10px;
  ${font}
`;
```

- если в хелпере будут изменяться стили в зависмости от свойств компоненты, тогда нужно заимпортить css модуль из styled-component

```jsx
import styled, { css } from "styled-components";
```

Cоздадим хелпер который центрирует блок. Также сделаем возможным изменять положение блока например по вертикали, передав туда данные, которые получим из пропсов

```jsx
// CSS HELPERS
const centered = css`
  position: absolute;
  top: ${({ top }) => top + "%"};
  left: 50%;
  transform: translate(-50%, -50%);
  ${font}
`;
```
Используем этот хелпер например в медиа правилах.

```jsx 
const Button = styled.button`
  padding: 25px;
  background-color: palevioletred;
  border: none;
  margin: 20px;
  color: ${fontColor};
  // при минимальной ширине 900 пикселей к кнопке применится наш хелпер
  @media (min-width: 900px) {
    ${centered};
  }
`;
``` 

Обычно создается отдельный файл для миксинов, откуда они экспортируются. После в месте где они нужны производим импорт нужного миксина и используем

- как переменную `${mixinName}` если это обычный хелпер/миксин
- как вызов функции `${mixinName({arg1:val, arg2:val})}` если нужно передать аргументы

```jsx
export default styled(Footer)`
  padding: 10px;
  background-color: aquamarine;
  // Миксин как переменная
  p {
    text-align: center;
    ${font};
  }
  // Мииксн с аргументами. Переназначили дефолтный размер width
  div {
    ${mixinName({ w: "300px" })}
  }
`;
```

### Animations
 
Для CSS-анимации используем `keyframes` из либы

```js
import styled, { keyframes } from 'styled-components'

// Создаем анимацию
const rotate = keyframes`
  from {
    transform: rotate(0deg);
  }

  to {
    transform: rotate(360deg);
  } 
`;

// используем созданную анимацию
const Rotate = styled.div`
  display: inline-block;
  animation: ${rotate} 2s linear infinite;
  padding: 2rem 1rem;
    font-size: 1.2rem;
`;

render(
  <Rotate>&lt; 💅 &gt;</Rotate>
);
```


### Global_Styles

Для установки глобальных стилей, типа нормализации, шрифтов и т.п необходимо

- импортировать `createGlobalStyle` из `styled-components` 

- создать файл например `Globalstyles` где описать глобальный css правила 

```jsx
import { createGlobalStyle } from "styled-components";

const GlobalStyle = createGlobalStyle`
*{
  box-sizing:border-box;
}

body {
  margin: 0;
  padding: 0;
  font-family: Monospace,  sans-serif; 
  background-color: #ccc;
}`;

export default GlobalStyle;
``` 

- заимпортить переменную в `entry point` приложения и поместить в render метод

```jsx
import React from "react";
import Header from "./Header";
// global styles
import GlobalStyle from "./GlobalStyles";

function App() {
  return (
    <div className="App">
      <Header />
      <GlobalStyle />
    </div>
  );
}

export default App;
```
 

# Themes

Приложение оборачивается в контекст провайдер который инициализируется получив ссылку на тему, после чего все компоненты приложения имеют доступ к теме, которая доступна в поле `theme` объекта props.

```js 
//  Определим несколько полей в теме
const theme = {
  color: "palevioletred",
  background: "white",
  ....
};

// Передадим ссылку на тему в проп theme Провайдера
render(
  <ThemeProvider theme={theme}>
    <div>
      <Button>Default Theme</Button> 
      <Button>Inverted Theme</Button> 
    </div>
  </ThemeProvider>
);

// Используем для стилизации значения из темы
const Button = styled.button`
  color: ${props => props.theme.color};
  border: 2px solid ${props => props.theme.color};
  background: ${props => props.theme.background}; 
  font-size: 1em;
  margin: 1em;
  padding: 0.25em 1em;
  border-radius: 3px;
`;
```

В случае если компонент которому нужны данные из темы не обернут в контекст провайдера, то существует несколько способов получить доступ к объекту темы

1. С помощью хока `withTheme`, также создаст в пропсах ссылку на тему

```js
import { withTheme } from 'styled-components';

class MyComponent extends React.Component {
  render() {
    console.log('Current theme: ', this.props.theme);
    // ...
  }
}

export default withTheme(MyComponent);
```
2. С помощью Реакт хука `useContext`

```js 
import { useContext } from 'react';
import { ThemeContext } from 'styled-components';

const MyComponent = () => {
  const themeContext = useContext(ThemeContext);

  console.log('Current theme: ', themeContext);
  // ...
}
```
____

## Атрибуты

### attrs 

Для управления атрибутами элемента, сущуствует метод `attrs` который принимает аргументом ф-цию, в которой доступна ссылка на объект `props` в зависимости от значения которого можем сетить нужные атрибуты

- Пример с сеттингом атрибута и его значения в зависимости от `props` и дальнейшей стилизацией 

```js
const Input = styled.input.attrs(props => ({ 
  type: props.password ? "password" : "text,  
}))`
  color: palevioletred;
  font-size: 1em;
  border: 2px solid palevioletred;
  border-radius: 3px; 
  margin: ${props => props.size};
  padding: ${props => props.size};
`; 
```
Особенно полезно при работе с атрибутом style, для компонентов, которые часто  меняют свои стили в зависимости от `props`, благодаря этому не создаются каждый раз новые селекторы.

### as

Полиморфный prop `as` используется чтобы динамически поменять элемент, который получает твои стили.

В примере, отрендерится не `div` а `button` со стилями которые описаны для `div`. Подобные трюки можно использовать и с кастомными компонентами.

```js
const Component = styled.div`
  color: red;
`;

render(
  <Component
    as="button"
    onClick={() => alert('It works!')}
  >
    Hello World!
  </Component>
)
```
### css

Используем атрибут `css` если нет необходимости создавать отдельный `styled` компонент. 
Для поддержки нужен плагин [babel-plugin](#https://styled-components.com/docs/tooling#babel-plugin)

```js
<div
  css={`
    background: papayawhip;
    color: ${props => props.theme.colors.text};
  `}
/>

<Button css="padding: 0.5em 1em;" />
```
Под капотом создадутся `styled` компоненты с соответствующими css правилами

```js
const StyledDiv = styled.div`
  background: papayawhip;
  color: ${props => props.theme.colors.text};
`

const StyledButton = styled(Button)`
  padding: 0.5em 1em;
`
```
 
## HOC

### styled

Оборачиваем созданный компонент , стилизуем его и експортируем дальше

```jsx
import React from "react";
import styled from "styled-components";

const Footer = ({ className }) => {
  return (
    <footer className={className}>
      <p>This is footer app</p>
    </footer>
  );
};

// Обернем его в styled HOC и зададим стили для всего блока и для вложенных также

export default styled(Footer)`
  padding: 10px;
  background-color: aquamarine;
  p {
    text-align: center;
  }
`;
```

### withTheme

Обернутый компонент получает в своих `props` доступ к текущей теме в свойстве `theme`

```js
import { withTheme } from 'styled-components'

class MyComponent extends React.Component {
  render() {
    console.log('Current theme: ', this.props.theme)
    // ...
  }
}

export default withTheme(MyComponent)
```
 



### Under_the_hood

Когда впервые импортируется библиотека в приложение, она создает внутреннюю переменную-счетчик для подсчета всех компонентов, созданных с помощью `styled`  

Когда `styled-components` создает новый компонент
- инкрементируется счетчик  
- создается внутренний идентификатор `componentId`, который составляется из разных значений  

```js
counter++;
const componentId = 'sc-' + hash('sc' + counter); // на выходе получим sc-bdVaJa
```

Как только идентификатор создан, `styled-components` вставляет  
- новый HTML элемент `<style> ` в `<head>`  
- добавляет специальный маркер комментария с **componentId** к элементу, который будет использоваться позже.  

```html
<style data-styled-components>
  /* sc-component-id: sc-bdVaJa */
</style>
```

Когда новый компонент создан  
- **componentId**   
- и целевой компонент, который мы передали в `styled`   
сохраняются в статических полях  

```js
StyledComponent.componentId = componentId;
StyledComponent.target = TargetComponent;
```

> Компоненты, созданные с помощью `styled` наследуются от класса `BaseStyledComponent`, который реализует несколько методов жизненного цикла.

#### При маунте компонента **componentWillMount** 
`styled-component` запускает в свою очередь свой жизненный цикл 

1. Evaluating tagged template  
Оценка написанного в шаблонной строке (парсинг написанного css)  

2. Generating CSS class name  
Генерация уникального css className для компонеты, который состоит из хеша **componentId** и **оцененной шаблонной строки**  

В примере ниже, на выхое получим className вида **jsZVzX**  
```js
const className = hash(componentId + evaluatedStyles); 
```
> Затем это имя класса сохраняется в состоянии компонента как **generatedClassName** 

```js
this.state.generatedClassName
```

3. CSS Preprocessing  
CSS-препроцессор stylis получает валидную строку CSS

```js
// получим .jsZVzX
const selector = '.' + className;  

// ф-ция которая соединит написанное в шаблонной строке с классом
const cssStr = stylis(selector, evaluatedStyles); 
```

В результате получим валидный css

```css
.jsZVzX {
  font-size: 24px;
  color: coral;
  padding: 0.25rem 1rem;
  border: solid 2px coral;
  border-radius: 3px;
  margin: 0.5rem;
}
.jsZVzX:hover{
  background-color: bisque;
}
```

4. Injecting CSS string into the page  
Теперь CSS должен быть вставлен в элемент <style> в <head> страницы сразу после маркера комментария компонента:

```html
<style data-styled-components>
  /* sc-component-id: sc-bdVaJa */
  .sc-bdVaJa {} 

  /* сгенерированные css селекторы с правилами */
  .jsZVzX{ 
    font-size:24px;
    color:coral; 
  }  

  .jsZVzX:hover{
    background-color:bisque;
  }
</style>
```

#### При рендере компоненты. Вызов метода **render()**

После того, как все закончилось с CSS, `styleled-components` просто нужно создать элемент с соответствующим className:

```js
//  Получаем те поля которые были созданы ранее
const TargetComponent = this.constructor.target; 
const componentId = this.constructor.componentId;
const generatedClassName = this.state.generatedClassName;

// формируем className из 
return ( 
  <TargetComponent 
    {...this.props} 
    className={this.props.className + ' ' + componentId + ' ' + generatedClassName}
  />
);
```
`styled-components` составляет className основываясь на    
- props.className - необязательно, передается родительским компонентом.   
- componentId - уникальный идентификатор компонента   
- generateClassName - uniq для каждого экземпляра компонента, который имеет действующие правила CSS

Выглядит так 

```js
<button class="sc-bdVaJa jsZVzX">I'm a button</button>
```

### При изменении `props` компоненты . **componentWillReceiveProps**.

При изменении props компонент перерендеривается, если наши css-стили зависят от props то будет сгенерирован весь цикл  событий как при **componentWillMount**  

1. Evaluate the tagged template.  
2. Generate the new CSS class name.  
3. Preprocess the styles with stylis.   
4. Inject the preprocessed CSS into the page.    

Если посмотреть в devTool на сгенерированный DOM то увидим, что для компоненты с id `sc-bdVaJa` генерируются каждый раз новый css-селектор. Изменяется `font-size`

```html
<style data-styled-components>
  /* sc-component-id: sc-bdVaJa */
  .sc-bdVaJa {} 
  .jsZVzX{font-size:24px;color:coral; } .jsZVzX:hover{background-color:bisque;}
  .kkRXUB{font-size:25px;color:coral; } .kkRXUB:hover{background-color:bisque;}
  .jvOYbh{font-size:26px;color:coral; } .jvOYbh:hover{background-color:bisque;}
  .ljDvEV{font-size:27px;color:coral; } .ljDvEV:hover{background-color:bisque;}
</style>
```

_________________________ 
