# react-pit-of-success

### Considerations
This will be a bit yadayada-y if you want, you can skip to the [meat](#index)  
This repo is an oppinionated guide on how to fall into the pit-of-success while working in react. Many times you'll google how to implement certain behaviour in react and the first solution will be the worst making you fall into the pit, this repo intends to fix this by beeing a one stop shop on the best way to handle certain behaviour. This advice is specific to react spas but some concepts might be transferable to fancier tech like nextjs or remix.   
Code exapmles may not "compile", most of them are pseudocode explain a point  
Even though this is an opinionated repo, my opinion can, and will change. I'll be very happy to read your PR changing this text and explaining why you disagree!

## Index

[considerations](#considerations)  
[starting from scratch](#starting-from-scratch)  
[on useEffect](#on-useeffect)  
[components as the base of abstraction](#components-as-the-unit-of-abstraction)  
[styling](#styling)  
[data fetching](#data-fetching)  
[validation](#validation)  
[forms](#forms)  
[routing](#routing)

### Starting from scratch
Do:
  - Use bun or pnpm
    - Pick one, I like bun but either way your setting yourself up for success
  - Use [vite](https://vitejs.dev/guide/) to bootstrap your app
    - Fast, well maintained, well configured out of the box, every dependency you need will have first class vite support
    - `bun create vite`
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
    - Prefer react-hook-form

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

**Components as the unit of abstration** benefits are very obvious when it comes styles but it is applicable to many other concepts. Maybe I'll write another example here using react-query as the subject


### Styling
Do:
- Check shadcn/ui before implementing it yourself
  - The rest of the advice was mainly gathered by reading shadcn/ui code
- Learn CSS
- Use tailwind
- Have an automated way to sort your tailwind classes
  - CSS class priority rant [here](#css-class-priority-rant)
- Use the `cn` helper
  - CSS class priority rant [here](#css-class-priority-rant), tldr; `cn` applies the classes you want in the correct order
```ts
// util/cn.ts
import { clsx, type ClassValue } from "clsx"
import { twMerge } from "tailwind-merge"

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs))
}
```
- Use variants ([cva]())
- Accept components as props
  - Sometimes it's easier to just accept and entire component and put it in the right slot than adding a million props to customize behaviour
- Accept class overrides as a prop
```jsx
const MyDiv = ({ className }) => <div className={cn("bg-black". className)}/>
...
<MyDiv className="bg-white"/>
```
- Use flexbox & grid layout
- Avoid margins like the plague
  - [assuming you can read](https://www.joshwcomeau.com/css/rules-of-margin-collapse/) | [assuming you can't](https://www.youtube.com/watch?v=KVQMoEFUee8)
- Use gap
  - just, DO IT!
- Use rem for font sizes
  - It's easy to use and accessible by default
- Wrap all the things
  - Responsivity by wrapping is 9 times out of 10 the best way to do it

Don't:
- Use plain css
  - Except when you want your styles to be REALLY global, like font-family for instance
- Use prebuilt templates, specially bad ones
  - Things to look out for:
    - Use of `!important`
    - Styling of html tags instead of classes
    - Scss black magic
- Override tailwind defaults
  - if you need something that's not default, extend it instead. Overriding the defaults will make it so that other tools that use tailwind defaults (like shadcn/ui or flowbite) wont work when you decide to use them
- Override rem definition
  - [assuming you can read](https://adrianroselli.com/2024/03/the-ultimate-ideal-bestest-base-font-size-that-everyone-is-keeping-a-secret-especially-chet.html) | [assuming you can't](https://www.youtube.com/watch?v=rg3zgQ3xBRc)

#### CSS class priority rant
The priority of styles is defined by the order classes appear on the css, not by the order they appear on the html
```css
.a { background: black }
.b { background: white }
```
```jsx
<div className="a b" /> // produces a white div
<div className="b a" /> // also produces a white div 
```
### Data fetching
🚧
### Validation
🚧
### Forms
🚧
### Routing
🚧




#### Todo:
- [ ] Add react query exapmle [here](#components-as-the-unit-of-abstraction)
- [ ] Add links to popular tools and docs