# angular-material-wrap-row-space-hack

[Angular - Material](https://material.angularjs.org/) uses a flex-box. There are a number of options to
align element. So you can align elements as a **row** and add **layout-wrap** so  elements fill few rows.

As also we can add **layout-align** property - which can have  *'start', 'center', 'end', 'space-around', 'space-between'*.

And there is a little ugly thing when you use *layout-wrap* and *space-between/around* together.
If the last row doesn't have the same number of elements as the previous one, it will align
element differently.

So I suggest to add empty node elements to fix this problem.
This solutions is not really universal. Because you can use different types of elements, which probably
can have required children elements. But still it is a bugfix and you can easily modify it for your
needs.


*Wrong*

![version 1.0][logo]

[logo]: https://github.com/AlexanderKozhevin/betterMe/blob/master/wrong.png?raw=true "Version 1.0"


*Right*

![version 1.0][logo]

[logo]: https://github.com/AlexanderKozhevin/betterMe/blob/master/right.png?raw=true "Version 1.0"

[**Ready to go JSFiddle sample**](https://jsfiddle.net/AlexanderKozhevin/ffcgv6om/)



*Solution*

```
app.directive("rowWrapFill", function($compile){
	return {
    restrict: "A",
  	scope: {},
    link: function(scope, elm, attrs){
			if (scope.$parent.$last){
        parent_width = elm[0].parentNode.clientWidth;
				child_width = elm[0].clientWidth;
				length = scope.$parent.$index + 1;
        if (length>0){
        	var theStyle = window.getComputedStyle(elm[0], null);

          //If there is a border - recalculate chid width
          if (theStyle.boxSizing == 'border-box'){
          	var border = theStyle.borderWidth;
            border = parseInt(border.replace('px', ''));
            child_width += border * 2;
          }

          var max_column = Math.floor(parent_width / child_width);

          //Calculate number of elements to add
          var temp = length % max_column
          if (temp != 0){
          	var to_add = Math.abs(max_column - temp);

            //And finally just add required number of empty elements to align elements the right way.
            for (var i=0; i<to_add; i++){
              blank_node = '<div style="width: ' + child_width + 'px;box-sizing: border-box;"></div>';


              node = $compile(blank_node)(scope)
              elm.parent().append(node)
          }


          }

        }
      }



    }
  }
})

```
