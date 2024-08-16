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
This repo is an oppinionated guide on how to fall into the pit-of-success while working in react. Many times you'll google how to implement certain behaviour in react and the first solution will be the worst making you fall into the pit, this repo intends to fix this by beeing a one stop shop on the best way to handle certain behaviour. This advice is specific to react spas but some concepts might be transferable to fancier tech like nextjs or remix

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