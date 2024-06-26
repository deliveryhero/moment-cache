# moment-cache

### Moment-cache is a tiny tool to speed up your moment.js calls.

During the app lifecycle we can call moment oftentimes. Every call is time. Time is **performance**. This tool will increase performance of your app by **caching moment.js instances**.

### Why?

```javascript
import moment from "moment";
import cache from "moment-cache";

const momentCalls = 99999;
const invalidDateStringWithOffset = "2013-02-08 09+07:00";
const dateStringWithOffset = "2020-05-12T23:50:21.817Z";
const timestamp = 628021800000;
const timeStampDate = "1989-11-25T18:30:00.000Z";

const dateString = "18-03-2024";
const format = "DD-MM-YYYY";

const check = (instance, ...args) => {
  let i = 0;
  const start = new Date();
  while (i <= momentCalls) {
    instance(...args);
    i++;
  }
  return new Date() - start;
};

const cacheTimestamp = check(cache, timestamp); // ~42 ms
const momentTimestamp = check(moment, timestamp); // ~49 ms

const cacheDateString = check(cache, dateString, format); // ~65 ms
const momentDateString = check(moment, dateString, format); // ~390 ms

const cacheDateStringWithOffset = check(cache, dateStringWithOffset); // ~218 ms
const momentDateStringWithOffset = check(moment, dateStringWithOffset); // ~777 ms

const cacheInvalidDateStringWithOffset = check(
  cache,
  invalidDateStringWithOffset
); // ~75 ms
const momentInvalidDateStringWithOffset = check(
  moment,
  invalidDateStringWithOffset
); // ~571 ms

const cacheTimestampDate = check(cache, timeStampDate); // ~202 ms
const momentTimestampDate = check(moment, timeStampDate); // ~755 ms

```

### Syntax:

#### Arguments:

- date: See [moment/parse](http://momentjs.com/docs/#/parsing/).
  <br/><b>If the date argument is a string in [date only form](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date#:~:text=Date%2Donly%20form%3A%20YYYY%2C%20YYYY%2DMM%2C%20YYYY%2DMM%2DDD), it won't be cached since key value can be [wrong without offset](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Date#:~:text=When%20the%20time,Reality%20Issue)</b>.

- format: See [moment/format](http://momentjs.com/docs/#/parsing/string-format/).

- clone (by default - **true**): set _false_ if you are not going to change instance in future. Will increase performance, but any object changes will affect cached result. See [moment/clone](http://momentjs.com/docs/#/parsing/moment-clone/).

```javascript
import cache from "moment-cache"; // or moment().cache
const myDate = "06-28-2016";
const format = "MM-DD-YYYY";
const date = cache(myDate, format); // moment.js cached instance
const anotherDate = cache(myDate, format); // rapidly retrieving previously processed result from the cache
```

#### Methods:

**updateStorage**: change cache destination.

###### **Arguments**:

- **storage**: object where cache data is stored. By default - covert object behind the scenes.

```javascript
import cachable from "moment-cache";
const myStorage = {};
cachable.updateStorage(myStorage);
const date = cachable("23-08-2016", "DD-MM-YYYY");
console.log(myStorage); // {23-08-2016 DD-MM-YYYY: MomentObject}
```
