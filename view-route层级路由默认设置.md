```
const routes = [
    path: '/home',
    components: {
        default: HomeBase
    },
    children: [
        {
            path: '/home',
            component: HomeIndex,
            name: "homeindex"
        },
        {
            path: ':aid',
            component: HomeArticle,
            name: "aid"
        }
    ]
]
```

