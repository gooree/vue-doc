# vue-ls

Vue plugin for work with local storage, session storage and memory storage from Vue context.

## Usage
```
import Storage from 'vue-ls';

// 配置项
options = {
  namespace: 'vuejs__', // key prefix
  name: 'ls', // name variable Vue.[ls] or this.[$ls],
  storage: 'local', // storage name session, local, memory
};
 
Vue.use(Storage, options);
 
new Vue({
    el: '#app',
    mounted: function() {
        Vue.ls.set('foo', 'boo');
        //Set expire for item
        Vue.ls.set('foo', 'boo', 60 * 60 * 1000); //expiry 1 hour
        Vue.ls.get('foo');
        Vue.ls.get('boo', 10); //if not set boo returned default 10
        
        let callback = (val, oldVal, uri) => {
          console.log('localStorage change', val);
        } 
        
        Vue.ls.on('foo', callback) //watch change foo key and triggered callback
        Vue.ls.off('foo', callback) //unwatch
        
        Vue.ls.remove('foo');
    }
});
```

- 全局引用

    Vue.ls

- 上下文引用

    this.$ls

## API方法

- Vue.ls.get(name, def)

  Returns value under name in storage. Internally parses the value from JSON before returning it.

- Vue.ls.set(name, value, expire)

  Persists value under name in storage. Internally converts the value to JSON.

- Vue.ls.remove(name)

  Removes name from storage. Returns true if the property was successfully deleted, and false otherwise.

- Vue.ls.clear()

  Clears storage.

- Vue.ls.on(name, callback)

  Listen for changes persisted against name on other tabs. Triggers callback when a change occurs, passing the following arguments.

    1. newValue: the current value for name in storage, parsed from the persisted JSON
    2. oldValue: the old value for name in storage, parsed from the persisted JSON
    3. url: the url for the tab where the modification came from

- Vue.ls.off(name, callback)

  Removes a listener previously attached with Vue.ls.on(name, callback).

