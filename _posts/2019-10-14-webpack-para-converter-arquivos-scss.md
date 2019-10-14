---
layout: post
author: Eryx
title: Webpack para converter arquivos .SCSS
date: 08/10/2019
---

Este texto contém orientações para implementar a conversão de arquivos .SCSS utilizando loaders do Webpack.


# Implementação


Criar package.json 

	> npm init -y
	
Instalar webpack e dependências

	> npm install --save-dev webpack webpack-cli webpack-dev-server
	> npm install --save-dev style-loader css-loader sass-loader node-sass mini-css-extract-plugin 

Instalar babel-loaders 

	> npm install --save-dev @babel/core @babel/preset-env @babel/preset-react babel-loader
	
Habilitar e criar arquivo de configurações babel

	> npm install --global create-babelrc
	> create-babelrc

Modifique o arquivo .babelrc para utilizar os presets instalados.

	{
		"presets": ["@babel/preset-env", "@babel/preset-react"]
	}

Crie o arquivo webpack.config.js com o código a seguir.

	module.exports = {
		module: {
			rules: [
				{
					test: /\.jsx?$/,
					loader: 'babel-loader',
					exclude: /node_modules/
				},
				{
					test: /\.scss$/,
					use: [
						{
							loader: "style-loader" // creates style nodes from JS strings
						},
						{
							loader: "css-loader" // translates CSS into CommonJS
						},
						{
							loader: "sass-loader" // compiles Sass to CSS
						}
					]
				}
			]
		},
		resolve: {
		  extensions: ['.js', '.jsx']
		}
	}

Arquivo package.json

	{
	  "name": "webpackapp1",
	  "version": "1.0.0",
	  "description": "",
	  "main": "index.js",
	  "scripts": {
	  "scripts": {
		"prestart": "webpack -d",
		"start": "node app-server.js"
	  },
	  "keywords": [],
	  "author": "",
	  "license": "ISC",
	  "devDependencies": {
		"@babel/core": "^7.6.2",
		"@babel/preset-env": "^7.6.2",
		"@babel/preset-react": "^7.0.0",
		"babel-loader": "^8.0.6",
		"css-loader": "^3.2.0",
		"mini-css-extract-plugin": "^0.8.0",
		"node-sass": "^4.12.0",
		"sass-loader": "^8.0.0",
		"style-loader": "^1.0.0",
		"webpack": "^4.41.0",
		"webpack-cli": "^3.3.9",
		"webpack-dev-server": "^3.8.2"
	  },
	  "dependencies": {}
	}

Crie uma pasta src contendo os arquivos index.js e index.scss. O webpack por padrão avalia o conteúdo da pasta src e cria uma pasta dist com o resultado do processamento.

Conteúdo do arquivo index.js.

	import style from './index.scss';
	const obj = {
		hello: 'World'
	}
	const { hello } = obj
	console.log(hello)

Conteúdo do arquivo index.scss.

	$body-color: red;

	body {
	  color: $body-color;
	}

Execute o build em modo development.

	> npm run build -- --mode development



# Referências

https://webpack.js.org/loaders/sass-loader/

https://webpack.js.org/loaders/

https://webpack.js.org/concepts/


