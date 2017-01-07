* iOS Block 
* Android Block
* JavaScript Block


### JS Block 
	/*! 添加指示器事件 */
		var indicators = document.getElementsByTagName("li");
		for (var i = 0; i < indicators.length; i++) {
			 var trigger = function(){
				  var temp = i;
				 indicators[i].onclick = function() {
				 	alert(i);
				 	alert(temp);
				 }
			}
			trigger();
		}
		
### iOS Block
	typedef void(^TriggerBlock)(void);
	
	for (NSUInteger i = 0; i < _buttons.count; i++) {
        TriggerBlock trigger = ^(void) {
            NSUInteger temp = i ;
            [((UIButton *)_buttons[i]) bk_addEventHandler:^(id sender) {
                NSLog(@"i = %ld",i);
                NSLog(@"temp = %ld",temp);
            } forControlEvents:UIControlEventTouchUpInside];
        };
        trigger();
    }

### Android Block