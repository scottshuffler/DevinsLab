# RethinkDB Setup

## make a rethinkdb directory
``` mkdir rethinkdb && cd rethinkdb```

### install node and rethinkdb 
Rethink: 

``` source /etc/lsb-release && echo "deb http://download.rethinkdb.com/apt $DISTRIB_CODENAME main" | sudo tee /etc/apt/sources.list.d/rethinkdb.list && sudo apt-get update && sudo apt-get install rethinkdb ```

Node: 

``` curl -sL https://deb.nodesource.com/setup_6.x | sudo -E bash - && sudo apt-get install -y nodejs && npm install rethinkdb ```

### start-up rethinkdb
``` rethinkdb --bind all & 2&> /dev/null```
	
### go to rethinkdb dashboard in browser
* adbXX.cs.appstate.edu:8080 (replace XX with your virtual machine number)
	
* click on data explorer tab, copy and paste the create table command
``` r.db('test').tableCreate('restaurants') ```
	
### copy and paste insert script (all at once)
```
r.table('restaurants').insert([
	{address: {building: 1007, coord: [-73.856077, 40.848447], street: 'Morris Park Ave', zipcode: 10462}, borough: 'Bronx', cuisine: 'Bakery', grades: [{date: Date(1234567890000), grade: 'A', score: 2}, {date: Date(1378857600000), grade: 'A', score: 6}, {date: Date(1358985600000), grade: 'A', score: 10}, {date: Date(1322006400000), grade: 'A', score: 9}, {date: Date(1299715200000), grade: 'B', score: 14}], name: 'Morris Park Bake Shop', restaurant_id: 30075445},
	{address: {building: 469, coord: [-73.961704, 40.662942], street: 'Flatbush Avenue', zipcode: 11225}, borough: 'Brooklyn', cuisine: 'Hamburgers', grades: [{date: Date(1419897600000), grade: 'A', score: 8}, {date: Date(1404172800000), grade: 'B', score: 23}, {date: Date(1367280000000), grade: 'A', score: 12}, {date: Date(1336435200000), grade: 'A', score: 12}], name: 'Wendys', restaurant_id: 30112340},
	{address: {building: 351, coord: [-73.98513559999999, 40.7676919], street: 'West 57 Street', zipcode: 10019}, borough: 'Manhattan', cuisine: 'Irish', grades: [{date: Date(1409961600000), grade: 'A', score: 2}, {date: Date(1374451200000), grade: 'A', score: 11}, {date: Date(1343692800000), grade: 'A', score: 12}, {date: Date(1325116800000), grade: 'A', score: 12}], name: 'Dj Reynolds Pub And Restaurant', restaurant_id: 30191841},
	{address: {building: 2780, coord: [-73.98241999999999, 40.579505], street: 'Stillwell Avenue', zipcode: 11224}, borough: 'Brooklyn', cuisine: 'American' , grades: [{date: Date(1402358400000), grade: 'A', score: 5}, {date: Date(1370390400000), grade: 'A', score: 7}, {date: Date(1334275200000), grade: 'A', score: 12}, {date: Date(1318377600000), grade: 'A', score: 12}], name: 'Riviera Caterer', restaurant_id: 40356018},
	{address: {building: 9722, coord: [-73.8601152, 40.7311739], street: '63 Road', zipcode: 11374}, borough: 'Queens', cuisine: 'Jewish/Kosher', grades: [{date: Date(1416787200000), grade: 'Z', score: 20}, {date: Date(1358380800000), grade: 'A', score: 13}, {date: Date(1343865600000), grade: 'A', score: 13}, {date: Date(1323907200000), grade: 'B', score: 25}], name: 'Tov Kosher Kitchen', restaurant_id: 40356068}
])
  ```

### Make a new js file called realtime_feed.js with the following contents:
``` 
r = require('rethinkdb');

var connection = null;

r.connect( {host: 'localhost', port: 28015}, function(err, conn) {
  if (err) throw err;
  connection = conn;

  r.table('restaurants').changes().run(connection, function(err, cursor) {
    if (err) throw err;
    cursor.each(function(err, row) {
        if (err) throw err;
        console.log(JSON.stringify(row, null, 2));
    });
});

})
```
### start real-time feed
``` node realtime_feed.js ```
