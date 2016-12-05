# GAS Slide API  

## A library to manipulate Google Slide with Apps Script  
---
## Note  
Slide API GAS is still alpha, that mean that for now there is only a few methods to manipulate tables or to build request like in the advanced services of google apps script.  
Next steps are:  
 - images manipulation
 - text manipulation
 - ...
### install  
The installation process is done through 2 steps: adding the library and authorizing the scope for the library to be used.  
- install the library by adding it form the menu:  
Resources > Libraries...  
then add in the field 'Find a Library' the id:  
 **1XDkYy_U9mTwBQ964uLojuGoOqmSc0iicTuY5zFU3-dg_4sFq_2N8GrYH**  

- Authorize the scopes by going in the menu:  
Resources > Advanced Google Services...  
Switch Drive API "on" and then click the link *Google Developers Console*.
In the Google Developers Console enable the two following scopes:  
 - Google Drive API  
 - Google Slides API  


### Usage  
Once installed the library can be used by by calling SlideApi. Actually only two methods are supported *openById* and *createSlide* as well as the class *presentations* that is the equivalent of the advanced services of google apps script. They should be available with autocompletion.  
Actually, Google Apps Script limitations do not allow autocompletion on sub methods. the reference table will help you to build the sub methods.  

#### sub methods reference table  
 - **getPageById()**



### Example  
this code will create a new presnetation and add it some tables with style.
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
