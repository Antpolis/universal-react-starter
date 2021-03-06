### Let's get started

`/client/src/component`, contains dumb React components which depend on containers for data.

`/client/src/container`, contains React components which are connected to the redux store.

***Container components care about how things work, while components care about how things look.***

#### Project Structure

```javascript

setup-isomorphic-javascript
	client
		 _src
 			component // dumb component (stateless component)
			container // smart component connected with redux store
			reducers.js // config reducer
			routers.js // config router
		App // layout + provider
		index.html
		index.js // entry point
	server
		bin
		db
			model // schemas
			index.js // config db
		public // static files
		routes // source router
		views // jade files
		app.js // entry point
	package.json
	webpack.config.js // config webpack
	public //static file

```

#### component & container structure

```javascript

component
	Header
		index.js // entry component
		style.css // styling for component

container
	Home
		action.js // actions for dispatching store
		constants.js // action type
		index.js // entry container
		reducer.js // container's reducer
		style.css // styling for container

```

## START development

client-side :

```shell
npm run dev
```
bundle client-side :

```shell
npm run build
```
server-side :

```shell
npm run start
```


#### config router

`/client/src/routers.js`, config for routing, import component and set its path.

```javascript

import Profile from "./container/Profile";
import About from "./container/About";

export default [{
  path: '/about',
  component: About,
}, {
  path: '/profile',
  component: Profile,
}];

```

#### config reducer

`/client/src/reducers.js`, combine all reducers created in component's folder

```javascript

import { combineReducers } from 'redux';

import homeState from "./container/Home/reducer";
import aboutState from "./container/About/reducer";

export default combineReducers({
  homeState,
  aboutState,
});

```

#### config database
`/server/db/model`, exporting all schemas created in ```/server/db/model``` directory.

```javascript

const mongoose = require('mongoose');
const Headers = require('./headersSchema');
module.exports = {
  Headers,
};

```
```javascript

// sample schema
const mongoose = require('mongoose');
const headersSchema = mongoose.Schema({
  data: String,
});
const Headers = mongoose.model('Headers', headersSchema);

module.exports = Headers;

```

#### config API

`/server/app.js`

```javascript

const express = require('express');
const app = express();
const headers = require('./routes/headers');
// app source
app.use('/headers', headers);

```
`/server/source/`

```javascript

// sample API
var express = require('express');
var router = express.Router();
const { Headers } = require('../db/model');

const getHeaders = (callback) => {
  Headers.find({}, callback)
};

const addHeaders = (header, callback) => {
	const newHeaders = new Headers(header);
	newHeaders.save().then((newHeaders) => {
		callback(newHeaders);
	})
}

router.get('/', (req, res, next) => {
  getHeaders((err, headers) => {
		if(err){
			throw err;
		}
		res.json(headers);
	});
});

router.post('/', (req, res, next) => {
	const header = req.body;
  console.log(header)
	addHeaders(header, (newHeaders) => {
		res.json(newHeaders);
	});
});

module.exports = router;

```

#### config webpack

`/webpack.config.js`, This is the file that's going to create bundle file compile all the JavaScript and the JSx, and it's also going to launch the development server.

```javascript

var path = require('path');
var webpack = require('webpack');

module.exports = {
	entry: './client/index.js',
	output: {
		path: path.resolve(__dirname, './dist/'),
		filename: 'bundle.js',
		publicPath: '/'
	},
	devServer: {
		inline: true,
		port: 50044,
		historyApiFallback: true
	},
	module: {
		loaders: [
			{
				test: /\.js$/,
				exclude: /node_modules/,
				loader: 'babel-loader',
				query: {
					presets: ['es2015', 'react', 'stage-0'],
					plugins: [
					    'react-html-attrs',
					    'transform-class-properties',
					    'transform-decorators-legacy'
					]
				}
			},
			{
				test: /\.css$/,
				exclude: /node_modules/,
				loader: 'style-loader!css-loader?sourceMap&modules=true&localIdentName=[name]__[local]___[hash:base64:5]'
			},
		]
	},
	plugins: [
		new webpack.DefinePlugin({
    	    'process.env': {
      	        'NODE_ENV': process.env.NODE_ENV
    	    }
  	    })
	]
}

```

> *Hit me up if you have any questions regarding the project > nasser.maronie@gmail.com*
