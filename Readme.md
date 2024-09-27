# react-pit-of-success
## Index

[considerations]()  
[starting from scratch]()  
[on useEffect]()  
[components as the base of abstraction]()  
[styling your components]()  
[handling data fetching]()  
[handling validation]()  
[handling forms]()  
[routing]()


### Considerations
This will be a bit yadayada-y if you want, you can skip to the [meat]()  
This repo is an oppinionated guide on how to fall into the pit-of-success while working in react. Many times you'll google how to implement certain behaviour in react and the first solution will be the worst making you fall into the pit, this repo intends to fix this by beeing a one stop shop on the best way to handle certain behaviour. This advice is specific to react spas but some concepts might be transferable to fancier tech like nextjs or remix.  
Code exapmles may not "compile", most of them are pseudocode explain a point

### Starting from scratch
Do:
  - Use bun or pnpm
    - Pick one, I like bun but either way your setting yourself up for success
  - Use [vite]() to bootstrap your app
    - Fast, well maintained, well configured out of the box, every dependency you need will have first class vite support
    - `vite ...`
  - Use typescript 
    - ...
  - Use an empty prettierrc.json
    - Any prettier configuration is a waste of time, the defaults are very sane. Even if you disagree tanking `somewhat ugly` code is much better than 2h of meetings discussing trailing commas and tabsize
  - Configure eslint with the react compiler plugin
    - It will warn you every time you make a mistake that's not obvious
  - Configure strict mode
    - Great for debugging with 0 cost 
  - Configure internationalization
    - Configuring internationalization from the project start will save you countless hours of ripping every single string out of your project later

Don't:
  - Use create-react-app
    - Webpack might be a fine alternative to vite but I'd avoid CRA specifically 
  - Use yarn
    - elaborate...

## On useEffect
Do:
  - Use it when you know you need it and there's no reasonable alternative
  - Use it when library maintainers recommend you do and provide an example implementation
    - Examples would be @tanstack/query deprecating onSuccess in favour of useEffect
  - Accept the eslint dependecy suggestions
    - If eslint disagrees with you and you are not @TkDodo, you are probably wrong
Don't:
  - Use it to fetch data
    - Prefer react-query
  - Use it to handle form validation

## Components as the unit of abstraction
This idea is at the core of what makes react great, composability of components.

It goes against
- Traditional separation of concerns - You separate your code based on wether it's part of your site's structure (`html`), styles (`css`) and logic (`js`). 

It gives you instead
- Separation of behaviour - Your component can be mounted anywhere regardless of context
- Locality of behaviour - Your component can be understood without context of the surrounding components

Do:
- Write your components in a way that makes them mountable anywhere

Example 1:  
**Separation of concerns** means that to understand whether this button is black with white text or white with black text you'll:
- Open the jsx file
- ripgrep whatever css mess you've created to find .btn and all it's variants
- Look for the closest `<nav>`
- Check if it is the 1st parent of `<MyButton>`
- Pray nobody else pulled a `!important` on you
```css
nav .btn {
  background: black;
  color: white;
}
.btn {
  background: white;
  color: black;
}
```
```jsx
const MyButton = () => (
  <button className="btn">
    My Button 
  </button>
)
...
<MyButton />
```
Example 2:  
**Separation of behaviour** allows you to have a button with the styles on nav wherever  
**Locality of behaviour** means you know (with certainty) what color the button will be. Like in the previous example this can get more complex with styles on top of styles but the worst case scenario you'll be 2 control clicks away of any info you need. 
- Look at `<MyButton/>`, and what the `variant` prop is
- ctrl+click `<MyButton/>`
- ctrl+click the function that receives `variant`
Most of the times the code will even fit in a single screen
```jsx
import { cva } from "class-variance-authority";
 
const myButtonVariants = cva({
  variants: {
    intent: {
      default: "bg-black",
      secondary: "bg-white",
    },
  },
  defaultVariants: {
    intent: "default",
  },
});
 
const MyButton = ({ variant }) => (
  <button className={myButtonVariants({ variant })}>
    My Button 
  </button>
)
...
<MyButton variant="nav"/> 
```

**Components as the uni of abstration** benefits are very obvious when it comes styles but it is applicable to many other concepts. Maybe I'll write another example here using react-query as the subject
