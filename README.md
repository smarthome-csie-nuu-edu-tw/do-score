## Project setup
```
yarn install
```

### Document root of web
```
/dist
```

### Run express server in Production mode
```
yarn express:run
```

### Default express server host of this project
```js
const ExpressServer = url => 'http://localhost:3000' + url;
```

**This Project is always build before push to git.**
