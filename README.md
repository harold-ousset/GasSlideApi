# GAS Slide API  

## A library to manipulate Google Presentation Slides with Google Apps Script  
---
## Note  
Slides API GAS is still alpha, that means that for now there are only a few methods to manipulate tables or to build requests like in the advanced services of google apps script.  

Next steps are:  
 - images manipulation
 - text manipulation
 - more control on the tables (style, text modifications...)
 - ...  

If you have any request, please do not hesitate to post it [here](https://docs.google.com/a/ousset.net/forms/d/e/1FAIpQLSclmB-SBMzXwyI5essEgaNhTi0fDaqcuryGL0evauJXZRiqfA/viewform)

### Installation  
The installation process is done through 2 steps:
Adding the library and authorizing the scope for the library to be used.  
- Adding the library: in your Google Apps Script do it from the menu:  
Resources > Libraries...  
<img src="http://i.imgur.com/isbBD4j.png" alt="ressources>libraries" style="width:200px;">  
Then in the field 'Find a Library'  add the id :  
 **1XDkYy_U9mTwBQ964uLojuGoOqmSc0iicTuY5zFU3-dg_4sFq_2N8GrYH**  
 ![Library installation](http://i.imgur.com/xony0kx.png)  

- Authorize the scopes by navigating in the menu:  
Resources > Advanced Google Services...  
<img alt="resources > advanced Google services..." src="http://i.imgur.com/vmQTBz0.png" style="width:200px">  
Switch Drive API "on" and then click the link *Google Developers Console*.  
![google dev console](http://i.imgur.com/vcFOPi3.png)  
In the Google Developers Console enable the two following scopes:  
 - Google Drive API  
 - Google Slides API  
 ![slide and drive api](http://i.imgur.com/Vqci1H4.png)


### Usage  
Once installed the library can be used by calling SlideApi. Currently only two methods are supported *openById* and *createSlide* as well as the class *presentations* that is the equivalent of the advanced services of Google Apps Script. They should be available with autocompletion.  

At the moment, Google Apps Script limitations do not allow autocompletion on sub-methods. The reference table will help you to build the sub-methods.  

#### sub methods reference table  
- **methods**  

| Method | Return type |
|--------------------------|-------------|
| openById(presentationId) | slideObject |
| createSlide(name) | SlideObject |

 * **sub-methods**  

SlideObject  

| Name | Return type | Brief description |
|---------------------|---------------------|---------------------------------------------|
| getPageById(pageId) | PageObject | a page object for chaining (pageId: String) |
| getPages() | Array of PageObject |  . |

PageObject  

| Name | Return type | Brief description |
|-------------------------------------|-------------|-----------------------------------------------------------------------------------------------|
| getPageId() | String | page id for ulterior identification |
| getName() | String | title of the slide |
| getSlideId() | String | id of the presentation |
| getElements() | Object | {tables:[tableObjects], images:[imageObject], shapes:[shapeObject], elemsCount:elems.length}; |
| createTable(rows, columns, options) | TableObject | options {id, height, width, left, top} |
| getTableById() | TableObject | . |
|createImage(url, width, height, options) | ImageObject | options {id, top, left}|
|createTetBox(width, height, options)| TextBoxObject|options {top, left}|

ImageObject  

|Name|Return type|Brief description|
|-------------------------------------|-------------|-----------------------------------------------------------------------------------------------|
| move(x, y, mode) | ImageObject | for chaining |

TextBoxObject  

|Name|Return type|Brief description|
|-------------------------------------|-------------|-----------------------------------------------------------------------------------------------|
|setText()|TextBoxObject|TextBox for chaining|
| move(x, y, mode) | TextObject | for chaining |

TableObject  

| Name | Return type | Brief description |
|---------------------------------------------|-------------|-------------------------------------------------------|
| getId() | String | id of the element |
| getType() | String | return 'table' |
| getPageId() | String | id of the page where the element belong |
| getSlideId() | String | id of the presentation |
| move(x, y, mode) | TableObject | for chaining |
| getRows() | number | number of rows in the table |
| getColumns() | number | number of columns in the table |
| getValues() | 2DArray | a 2D Array like the one you can get in SpreadsheetApp |
| getRange_(row, column, numRows, numColumns) | RangeObject | Range object for chaining |

RangeObject  

| Name | Return type | Brief description |
|---------------------------|-------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| getId() | String | id of the element |
| getPageId() | String | id of the page where the element belong |
| getSlideId() | String | id of the presentation |
| getValues() | 2DArray | a 2D Array like the one you can get in SpreadsheetApp |
| setValues(2DArray) | RangeObject | Range object for chaining |
| setBackgroundColor(r,g,b) | RangeObject | r: red color scale from 0 to 1 or 0 to 256 alternatively a #Color string <br>g: green scale from 0 to 1 or 0 to 256 <br>b: blue scale from 0 to 1 or 0 to 256 <br>/!\ to use the scale 0 to 256 at least one of the component must be higher than 1 |

 - **classes**  

| Name | Brief description |
|---------------|----------------------------------------------------------------|
| presentations | use the slideApi like the advanced Google Apps Script services |

##### presentations methods  

| Method | Return type | Brief description |
|------------------------------------------------|----------------|--------------------------------------------------------|
| get(presentationId) | responseObject |  |
| batchUpdate(presentationId,  fields,  request) | responseObject | presentationId: String fields: String, request: Object |
| create(fields,  request) | responseObject | fields: String, request: Object |
| pages | pageObject | . |

* **presentation.pages**  

| Method | Return type | Brief description |
|-------------------------------------------|----------------|-------------------|
| get(presentationId, pageObjectId, fields) | responseObject | . |


### Example  
this code will create a new presentation and add some tables with style.

```javascript
function launchSample() {
  // create a slide with name 'slide name'
  var slide = SlideApi.createSlide('slide name');
  // alternatively we could have used "slide = SlideApi.openById('slideId');""

  // retrieve the id of the created slide
  var slideId = slide.getId();

  // retrieve the pages of the slide
  var pages = slide.getPages();

  // get the last page
  var page =pages.pop();  
  Logger.log('got page ' + page.getName() + ' with id: ' + page.getPageId()); // return 'got page undefined with id: p' as there is no title and id of the first page is always 'p'

  // create a table with 5 rows and columns
  var table = page.createTable(5, 5);
  Logger.log('table id: ' + table.getId());

  // retrieve a range starting at row 2 and column 2 with size 3x3
  var rng = table.getRange(2,2,3,3);
  rng.setValues(testVals); // testVals is a 2DArray as the one you can retrieve from SpreadsheetApp
  rng.setBackgroundColor('#868A08');

  // get the elements of the page
  var pageElements = page.getElements(); // is an object {tables, images, shapes, elementCount}
  Logger.log('list page elements: ' + JSON.stringify(pageElements));

  // retrieve the last table of the presentation (the same as before but an other way)
  var tbl = pageElements.tables.pop();
  tbl.getRange(1,2,2,3).setBackgroundColor(0.6, 0.8, 0.6);
  tbl.moveTable(40, 50);
  //Logger.log('tbl values: '+tbl.getValues()); // return the whole table
  Logger.log(tbl.getRange(2,2,2,2).getValues()); // return [[ein, zvei], [uno, dos]];

  // create a table with options
  var table2 = page.createTable(2, 2, options); // options is an object that let you specify size and position
  table2.getRange(1,1,1,1).setBackgroundColor('#2E64FE');
  table2.getRange(2,1,1,1).setBackgroundColor('#8258FA');
  table2.getRange(1,2,1,1).setBackgroundColor('#FA5882');
  table2.getRange(2,2,1,1).setBackgroundColor('#DBA901');
}

```

#### presentation class example  
 This class will let you use the slideApi like the advanced Google Apps Script services.  
 ```javascript
 function testPresentations() {
   var presentationId = 'ID_OF_YOUR_PRESENTATION';
   var pageObjectId = 'ID_OF_A_PAGE'; // use 'p' for the first page
   Logger.log(SlideApi.presentations.get(presentationId));
   Logger.log(SlideApi.presentations.pages.get(presentationId, pageObjectId));
 }
 ```
