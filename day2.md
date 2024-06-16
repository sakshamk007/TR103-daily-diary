# Day 2

## 1. Node.js and NPM installation
1. Download from [nodejs.org](https://nodejs.org) and follow the installation instructions for your operating system. Check nodejs and npm version by running `node -v` and `npm -v` in terminal.

## 2. NPM 
1. Use Node Version Manager (nvm) to manage different versions of Node.js.
1. Follow instructions from [nvm-sh/nvm](https://github.com/nvm-sh/nvm).
1. Use commands `nvm list -remote`, `nvm install <version>` 

## 3. Installed Packages via npm
Use `npm list`

## 4. NPM and NVM difference
1. NPM: Node Package Manager used for installing and managing packages.
1. NVM: Node Version Manager used for managing multiple Node.js versions.

## 5. Semantic Versioning
1. Major: Significant changes, breaking backward compatibility.
1. Minor: New features, backward compatible.
1. Patch: Bug fixes, backward compatible.
1. ^ (Caret): Latest minor version.
1. ~ (Tilde): Latest patch version.

## 6. Global vs Local Packages
1. Global Packages: Installed system-wide, accessible from any project (`npm install -g <package>`).
1. Local Packages: Installed in the current project directory (`npm install <package>`).

## 7. Global Objects in JS and Node.js
1. Traditional JS - `window`, `document` (DOM).
1. Node.js:
    1. Global Objects: `global`, `process`.
    1. Built-in Modules: `fs` (File System), `events`, `http`, `os`, `setTimeout`, `setInterval`.
