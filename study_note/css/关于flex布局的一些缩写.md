#### flex-flow
flex-flow : flex-direction flex-wrap
```
flex-flow:row nowrap (默认值)
```

### flex
flex : flex-grow flex-shrink flex-basis
```
flex: 0 1 auto (默认值)
```
该属性有两个快捷值：auto (1 1 auto) 和 none (0 0 auto)。

+ [flex-basis](https://developer.mozilla.org/en-US/docs/Web/CSS/flex-basis)

  **与「width」属性使用相同的值** 的此部件可以用来设置「flex-basis」长写并指定伸缩基准值，也就是根据可伸缩比率计算出剩余空间的分布之前，**伸缩项目主轴长度的起始数值**。若在「flex」缩写省略了此部件，则「flex-basis」的指定值是长度零。
  
  摘自[w3.org](https://www.w3.org/html/ig/zh/css-flex-1/#flex-common)

flex:1 等同
```
 flex-grow: 1;
 flex-shrink: 1;
 flex-basis: 0%;
```  
flex: 0% 等同
```
flex-grow: 1;
flex-shrink: 1;
flex-basis: 0%;
```
