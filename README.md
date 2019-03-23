# The New Dynamic Hugo Starter

Test. This is a starter repo for The New Dynamic's web projects. Do with as you wish, but keep in mind that everything here is designed for our internal workflow.

We use a [Changelog](https://github.com/theNewDynamic/hugo-starter/blob/master/CHANGELOG.md) on all of our projects. Please see that file for updates.

## Quickstart

_note this quickstart is for established projects; not the starter._

`git clone --recurse-submodules https://github.com/theNewDynamic/yourprojectname.git yourprojectname`  
`yarn install`  
`yarn start`

## Deployment

1. **Before deploying, run `yarn deploy` (or `hugo --gc` if you're not generating fonts or JS) to generate a minified, purged CSS file.**
2. Check the `resources` folder into your repository.


## tools

- [Hugo](http://gohugo.io/), a general purpose website framework
- [Parcel](https://parceljs.org/) - A "blazing" fast, zero configuration web application bundler.
- [PostCSS](https://postcss.org/)
- [TailwindsCSS](https://tailwindcss.com/) (library of JS-based CSS classes )
- [PurgeCSS](https://www.purgecss.com/) (removes unused CSS)
- [Quicklink](https://github.com/GoogleChromeLabs/quicklink/) Pre-fetch links on a page
- [Lazysizes](https://github.com/aFarkas/lazysizes) (lazy asset loading)
- [KyleAMathews/typefaces](https://github.com/KyleAMathews/typefaces) (load OS fonts locally with one little `require`)
- [HTMLTest](https://github.com/wjdp/htmltest) - A Go-based link HTML linter for links, images, scripts references.



### Fonts

When possible, we use Kyle Mathews' [Typefaces](https://github.com/KyleAMathews/typefaces) so that we can server our font files locally. Just add the typeface to `assets/index.js` and run the webpack build, as above (`yarn build`). This will generate the font files into the `static/fonts` directory (which Hugo will automatically copy into the public directory) and generate a fonts CSS file, which then Hugo will minify and fingerprint into your head.

### CSS

We use TailwindCSS and several PostCSS plugins to generate CSS. Most of your CSS will be utility classes in your templates. Hugo will not [rebuild](https://discourse.gohugo.io/t/regenerating-assets-directory-for-hugo-pipes/13175) your CSS file unless the file itself is changed. In a TailwindCSS context, most of your work occurs in templates, not in the CSS file. To make development easier, we've created a separate PostCSS [config](https://github.com/postcss/postcss-cli#config) file without PurgeCSS. However, this means you must run `yarn deploy` (as outline below) for you push your changes.


## Testing

Download htmltest to your local environment, build your site locall (to the public folder), and run `htmltest` in your site's path. If you need to change any parameters of the test, you'll find them in `.htmltest.yml`

# Hugo Asset Pipline

This set up utilizes Parcel for Javascript and open source font processing, Hugo Pipes + PostCSS for CSS processing, and `npm-run-all` to run Parcel and Hugo in parallel (see note below). We use the Yarn package manager, but you can use NPM if you like. This configuration is built around the assumption that JS/Fonts are processed in development, not production, though it would take minimal effort to do so.

## JS

- Uses Parcel Bundler.
- Parcel outputs to `assets/output/index.js`.
- Hugo is used to fingerprint and make a secure hash for Subresource Integrity.
  - If `fileExists "./assets/output/index.js` Hugo creates a hash of that file from `layouts/_head/scripts.html`.
- Hugo outputs the processed JS file to `public/output/index.min.[hash].js`.

## CSS

- ~Uses Hugo Pipes, utlizing PostCSS. NOTE: we use this method because we use utility-class CSS. I otherwise recommend you use Parcel to process your CSS. See "How to Switch to running JS-based PostCSS" below.~
- Uses Parcel/PostCSS as default. See "How to Switch to Hugo-based PostCSS" below.

### CSS via Parcel

- Parcel processes `assets/css/styles.css` with `assets/postcss.config.js`, utilizing Imports, TailwindCSS, Autoprefixer, and PurgeCSS.
- **If `NODE_ENV=development` is present in the build command, PostCSS will NOT process the file through PurgeCSS.**
- Hugo outputs the processed CSS file to `assets/output/index.[hash].css`.

## Fonts

- Open source fonts via [Typefaces](https://github.com/KyleAMathews/typefaces) uses Parcel Bundler.
- Parcel outputs CSS to `assets/output/index.css` and also puts the font files, hashed, in the same directory.
- Hugo Pipes processes files into public directory and create Prefetch links.
  - If `fileExists "./assets/output/index.css` Hugo creates the prefetch links from `layouts/_head/stylesheets.html`.
- Hugo outputs the processed fonts and CSS file to `public/output/index.min.[hash].css` and `public/output/font-name.[hash].woff[2]`.

## Images

- Assets stored in an S3 bucket or local to the repo, transformed via Imgix.

### Other Notes

- PurgeCSS will fail if any of your templates are empty.
- If you add themes, you may need to add the file location in the PurgeCSS `content` configuration item. This is done for you, with a glob pattern, but it is something to be aware of.


## To Use

`git clone https://github.com/theNewDynamic/hugo-starter.git yourprojectname`

`cd yourprojectname`

`rm -rf .git`

`git init`

`git add .`

`git commit -m "initial commit"`

`git remote add origin https://github.com/theNewDynamic/YOURPROJECTNAME.git`

`git push -u origin master`

`git submodule add https://github.com/theNewDynamic/hugo-layout_module-base-2019.git components/layout_module-base`

repeat for additional modules

Add [theme component](https://gohugo.io/themes/theme-components) to config.toml:

- `theme = ["layout_module-base", "layout_module-nameofmodule"]`

_Note. Hugo uses the "themes" directory by default. We've renamed that to `layout_modules`._