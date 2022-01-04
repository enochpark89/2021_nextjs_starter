# NextJS starter

# Route in nextJS

1. How do you create routes or pages?

- If you create index.js in the pages folder, it will create Home route without implementing a router.
- The name of the file becomes an URL.

2. What is pre-rendering in NextJS?

- For client service rednering - if the user has a slow PC, it will take some time. Also, JavaScript needs to be enabled though it is not common.
- However, with the NextJS, it uses the real HTML. The user will be able to see the site even if the JavaScript is disabled. They will see even if they do not have a connection. Hydration
    - render > HTML > show to the user.
- pre-generated state with HTML shown by users. Then, other components of JavaScript renders.
- This is really good for SEO.


