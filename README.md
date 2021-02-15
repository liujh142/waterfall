# waterfall
1. 获得数据
2. 将数据拼装成node并插入页面，通过瀑布流摆放位置
3. 滚动到底部 ————>>> 1. 获得数据

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0, maximum-scale = 1">
    <script src="https://cdn.bootcdn.net/ajax/libs/jquery/3.5.1/jquery.js"></script>
    <link rel="stylesheet" href="css/index.css">
    <title>Document</title>
</head>
<body>
    <div class="wrap">
   <div class="ct-waterfall">
       <ul id="pic-ct" class="clearfex">
           <!-- <li class="item">
               <a href="#" class="link">
                   <img src="http://s.img.mix.sina.com.cn/auto/resize?img=http%3A%2F%2Fwww.sinaimg.cn%2Fdy%2Fslidenews%2F5_img%2F2016_09%2F453_75615_657883.jpg&size=250_0" alt="">
               </a>
               <h4 class="header">标题</h4>
               <p class="desp">
                   当地时间2016年3月1日，在东部与亲俄武装作战中受伤的乌克兰士兵接受海豚治疗。
               </p>
           </li> -->

               <!-- 用于计算 item 宽度和列数，但不展示出来-->
               <li class="item hide"></li>
       </ul>
       <div id="load">我是看不见的</div>
   </div>
   <script>
       var isLoading = false;
       var colSumHeight =[];
       var nodeWidth = $('.item').outerWidth(true);
       var colNumber = Math.floor($('#pic-ct').width() / nodeWidth)
       var pageIndex = 1;
        for(var i = 0;i < colNumber;i++ ){
            colSumHeight[i] = 0;
        }
        start();
        function start(){
            getData(function(newsList){
                $.each(newsList,function(idx,news){
                    var $node = getNode(news)
                    $node.find('img').on('load',function(){
                        $('#pic-ct').append($node);
                        waterFallPlace($node);
                    })
                })
            })
       }
       function getNode(item){
	        var tpl = ''
		        tpl += '<li class="item">';
		        tpl += ' <a href="'+ item.url +'" class="link"><img src="' + item.thumb + '" alt=""></a>';
		        tpl += ' <h4 class="header">'+ item.stitle +'</h4>';
		        tpl += '<p class="desp">' + item.title +'</p>';
		        tpl += '</li>';
	
	        return $(tpl)
        }
       function getData(callback){
            pageCount = 15;
            if(isLoading){return};
             isLoading = true;
            $.ajax({
                url : 'https://photo.sina.cn/aj/v2/index?cate=military',
                dataType : 'jsonp',
                jsonp : 'callback',
                data : {
                    pagesize : pageCount,
                    page : pageIndex
                }
            }).done(function(ret){
                console.log(ret.data)
                callback(ret.data);
                pageIndex++;
            }).always(function(){
                isLoading = false;
            })
       }

        function waterFallPlace($node){

            var idx = 0,
            minSumHeight = colSumHeight[0];

            for(var i=0;i<colSumHeight.length; i++){
                if(colSumHeight[i] < minSumHeight){
                idx = i;
                minSumHeight = colSumHeight[i];
                }
            }
            $node.css({
                left: nodeWidth*idx,
                top: minSumHeight,
                opacity: 1
            });

            colSumHeight[idx] = $node.outerHeight(true) + colSumHeight[idx];
            $('#pic-ct').height(Math.max.apply(null,colSumHeight));

        }
        $('#pic-ct').scroll(function(){
            if(isToButton){
                start();
            }
        })
        $(window).on('scroll',function(){
            if(isToBottom()){
                start();
            }
        })
        function isToBottom(){
            return $(window).scrollTop() + window.innerHeight  >= $('body').height();
        }
    </script>
   </div>
</body>

</html>

```