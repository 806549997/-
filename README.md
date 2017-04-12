# 事件委托的学习以及整理

###为什么用事件委托
遇到过这样一个题目，比如我们有成千上万个个li，怎样为每个li都添加相同的click点击事件，
可能我们会用for循环的方法，来遍历所有的li，然后给它们添加事件，代码如下  
`     

    <ul id="ul">  
        <li>11</li>
        <li>22</li>
        <li>33</li>
        <li>44</li>
        <li>55</li>
    </ul>
 
######js部分,实现点击li弹出内容

    const li = document.querySelectorAll('li'); 
    for (let i = 0; i < li.length; i++){
    li[i].onclick = function () {
        alert(this.innerHTML);
        }
    }  
    
 但是在JavaScript中，添加到页面上的事件处理程序数量将直接关系到页面的整体运行性能，
 因为需要不断的与dom节点进行交互，访问dom的次数越多，引起浏览器重绘与重排的次数也就越多，
 就会延长整个页面的交互就绪时间，如果要用事件委托，就会将所有的操作放到js程序里面，与dom的操作就只需要交互一次，
 这样就能大大的减少与dom的交互次数，提高性能；
 
 ###事件委托的原理
 事件委托是利用事件的冒泡原理来实现的，何为事件冒泡呢？就是事件从最深的节点开始，然后逐步向上传播事件，举个例子：页面上有这么一个节点树，
 div>ul>li>a;比如给最里面的a加一个click点击事件，那么这个事件就会一层一层的往外执行，执行顺序a>li>ul>div，
 有这样一个机制，那么我们给最外面的div加点击事件，那么里面的ul，li，a做点击事件的时候，都会冒泡到最外层的div上，
 所以都会触发，这就是事件委托，委托它们父级代为执行事件。  
 
 ###事件委托的实现

Event对象提供了一个属性叫target，可以返回事件的目标节点，我们成为事件源，也就是说，target就可以表示为当前的事件操作的dom，
但是不是真正操作dom，当然，这个是有兼容性的，标准浏览器用ev.target，IE浏览器用event.srcElement，此时只是获取了当前节点的位置，
并不知道是什么节点名称，这里我们用nodeName来获取具体是什么标签名，这个返回的是一个大写的，我们需要转成小写再做比较：
`     

    <ul id="ul">  
        <li>11</li>
        <li>22</li>
        <li>33</li>
        <li>44</li>
        <li>55</li>
    </ul>
 
######js部分,实现点击li弹出内容

    const ul = document.querySelector('#ul');  
    const btn = document.querySelector('#btn');
    ul.onclick = function (e) {
        const ev = e || window.event;
        const target = ev.target || ev.srcElement;
        if (target.nodeName.toLowerCase() == 'li'){
            alert(target.innerHTML)
        }
    } 
    
 ###target与currentTarget的区别
 上面提到了通过Event的target属，返回事件的目标节点，那么这个属性跟currentTarget的区别是什么呢
 ，先看一下以下代码  
    
    <div id="fa" onmousedown="getEventTrigger(event)">  
        <p id="son" onmousedown="getEventTrigger(event)">点我试试</p>
    </div>
    
 ######js代码，弹出目标节点 
     const fa = document.getElementById('fa');
     const son = document.getElementById('son');
     function getEventTrigger(event) {
         let x = event.currentTarget;
         let y = event.target;
         alert("currentTarget 指向： " + x.id + "， target指向：" + y.id);
     }  
     
     
 当点击id为son的p元素时执行的是p元素上绑定的事件，此时，事件监听的对象是p元素，目标也是p元素，
 currentTarget为p元素，target也是p元素；当事件冒泡到它的父级id为fa的元素div，触发了绑定在div上的事件，
 而这时，事件监听的对象是div，目标元素依然是p元素，也就是说，这时的currentTarget指向div，target依然指向p元素，由此我
 们可以得出：e.currentTarget指的是注册了事件监听器的对象，而e.target指的是该对象里的子对象，也是触发这个事件的对象！