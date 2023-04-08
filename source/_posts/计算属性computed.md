---
title: 计算属性
date: 2023-04-05
tags:
- 计算属性
categories:
- [vue]


---



计算属性是用来描述依赖响应式状态的复杂逻辑。

在以下代码中所写的逻辑太复杂，写到template中显得太臃肿，我们想要判断作者是否有写过书，可以通过计算属性来描述这个逻辑。

```vue
<template>
	<span>{{author.books.length>0?'yes':'no'}}</span>
</template>
<script setup>
    import {reactive} from 'vue'
	const author=reactive({
        name: 'jack',
        books:[
            'book1',
            'book2',
            'book3'
        ]
    })
</script>
```

这是使用了计算属性的代码.我的理解是把复杂的代码包装起来，如果多个地方要用到就不用每次都写这么多代码。

```vue
<template>
	<span>{{publish}}</span>
</template>
<script setup>
    import {reactive,computed} from 'vue'
	const author=reactive({
        name: 'jack',
        books:[
            'book1',
            'book2',
            'book3'
        ]
    })
    const pulish=computed(()=>{
        return author.books.length>0?'yes':'no';
    })
</script>
```

定义了一个publish计算属性，它返回值为ref，我们可以通过publish.value拿到计算结果，但ref在template中会自动添加.value。

Vue中的计算属性会自动追踪响应式依赖，publish依赖于author.books，当author.books改变时publish也会改变。