# BUILD-TOOLS-BUCHELI-COURSE-ESBUILD-REACT-PROJECT

## Project Setup
esbuild, like Webpack, is a Node package. If your project is not a Node.js project, remember that you can initialize it using:

```
npm init -y
```

If you are following along using the provided React app, the project is already a Node.js project. But you will need to install dependencies via:

```
npm install
```

Next, we will need to install esbuild. We can do this using npm like below:

```
npm install esbuild
```

## The Build Command
Let’s add a build command in the scripts section of package.json. Similar to Webpack, esbuild requires an entry point and an exit point. However, since there is no default
entry and exit point, we must explicitly state these in our build command.

```
{
  "scripts": {
    "build": "esbuild ./src/app.js --bundle --outdir=./public"
  }
}
```

In the code block above, the esbuild command runs with the entry point at ./app.js to bundle the app with the exit point at ./public. The --bundle flag needs to be explicitly 
stated as esbuild does not bundle files by default. Exit points can be set using either the --outdir flag to provide a directory for output files (--outdir=./public) or 
the --outfile flag to specify the name of a single output file (--outfile=./public/out.js).

Remember that you can run the build command with npm run build.

You will notice that you are getting the following error:

 ```
 > src/app.js:40:15: error: Unexpected "<"
    40 │         return <Carousel src={src} />;
       ╵                ^
```

This error is caused by esbuild encountering JSX code in a JavaScript file. We will address this in the next section.

## Loaders
Like with Webpack, we can define rules to process various file types in our web app. esbuild supports loading several file types without the need for external loaders that require additional installations. These include TypeScript, JSX, CSS, and images.

While Webpack loaders are defined via rules in its config file, esbuild specifies its loaders via the --loader flag in the build command. The example command below defines a rule for .ext files to use loadername loader.

```
esbuild ./app.js --bundle --loader:.ext=loadername --outdir=./public
```

## JSX
Our current build script throws an error because our app.js contains JSX. We could either change the file extension of app.js and Carousel.js to use .jsx extensions, or define a rule to use a JSX loader. We do this by adding a --loader flag to our build command in package.json.

```
{
  "scripts": {
    "build": "esbuild ./src/app.js --bundle --loader:.js=jsx --outdir=./public"
  }
}
```

Here we’ve set our --loader flag to use the jsx loader for files with the .js extention. Now JSX syntax will be enabled in our JavaScript files.

## Images

You will notice another error saying that there is no loader configured for .jpg files.

 ```
 > src/app.js:7:18: error: No loader is configured for ".jpg" files: src/assets/ocean.jpg
    7 │ import ocean from './assets/ocean.jpg';
      ╵    
 ```
This error is triggered by import statements for image files in app.js.

One way to import images is as external files. To do this, we will need to add a --loader flag to our build command in package.json.

```
{
  "scripts": {
    "build": "esbuild ./src/app.js --bundle --loader:.js=jsx --loader:.jpg=file --outdir=./public"
  }
}
```

Now our .jpg images will be exported into the output directory.

You will get another error since the web app also uses an .svg image.

``` > src/Carousel.js:2:16: error: No loader is configured for ".svg" files: src/sun.svg
    2 │ import sun from './sun.svg';
      ╵    
```

Let’s bundle this image as a Base64-encoded data URL. Importing an image as a data URL can be more efficient as the image gets embedded into our JavaScript code. This means that there is one less browser request and faster load time! To do this, we will need to add another --loader flag to our build command in package.json.

```
{
  "scripts": {
    "build": "esbuild ./src/app.js --bundle --loader:.js=jsx --loader:.jpg=file --loader:.svg=dataurl --outdir=./public"
  }
}
```

Now we’ve set our --loader flag to use the dataurl loader for files with the .svg extention. Our .svg files will now be converted as data URLs and embedded into our bundle.

## CSS
When CSS files are imported directly in JavaScript the import statement, esbuild will automatically gather all referenced CSS files and bundle them into a single CSS file at the exit point.

We import Carousel.css in Carousel.js like below:

```
import './Carousel.css';
```

esbuild doesn’t need any loaders specified in order to automatically bundle Carousel.css file at the exit point at ./public.

## Putting It All Together
Now that we’ve set up our loaders, running the build command should generate two files in ./public: app.js and app.css. Notice that these files have been linked in ./public/index.html. You can view the app by opening the index.html file in any web browser as esbuild doesn’t have its own dev server.

Our React app has been converted into static files that can be hosted without Node.js packages!
