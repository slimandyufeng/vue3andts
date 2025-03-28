以下是 **从零开始** 的完整实现方案，包含所有命令、配置和代码细节，适合新手逐步操作：

---

### **一、环境准备**
1. 安装 Node.js (>=16.x)
   - 官网下载：https://nodejs.org
2. 安装代码编辑器 (推荐 VSCode)
3. 打开终端 (Windows: CMD/PowerShell, Mac: Terminal)

---

### **二、创建项目目录**
```bash
mkdir grapes-demo
cd grapes-demo
```

---

### **三、客户端实现 (Vue 3 + Vite)**

#### 1. 创建 Vue 项目
```bash
npm create vite@latest client -- --template vue-ts
cd client
npm install
```

#### 2. 安装 GrapesJS
```bash
npm install grapesjs vue-axios
```

#### 3. 创建编辑器组件 `src/components/Editor.vue`
```vue
<script setup lang="ts">
import { onMounted, ref } from 'vue'
import grapesjs, { Editor } from 'grapesjs'
import axios from 'axios'

const editor = ref<Editor | null>(null)
const htmlPreview = ref('')

// 初始化编辑器
onMounted(() => {
  editor.value = grapesjs.init({
    container: '#editor',
    height: '600px',
    storageManager: false,
    plugins: [],
    components: '<div>初始内容</div>'
  })
})

// 保存数据到服务端
const saveData = async () => {
  const projectData = editor.value?.getProjectData()
  await axios.post('http://localhost:7001/api/save', projectData)
}

// 预览服务端渲染
const previewServer = async () => {
  const { data } = await axios.get('http://localhost:7001/api/render')
  htmlPreview.value = data
}
</script>

<template>
  <div class="container">
    <div id="editor"></div>
    <div class="controls">
      <button @click="saveData">保存</button>
      <button @click="previewServer">预览</button>
    </div>
    <div class="preview" v-html="htmlPreview"></div>
  </div>
</template>

<style scoped>
#editor {
  border: 1px solid #ccc;
  margin: 20px 0;
}
.preview {
  margin-top: 20px;
  padding: 15px;
  border: 1px dashed #666;
}
button {
  margin-right: 10px;
  padding: 8px 15px;
}
</style>
```

#### 4. 修改入口文件 `src/main.ts`
```typescript
import { createApp } from 'vue'
import App from './App.vue'
import axios from 'vue-axios'

const app = createApp(App)
app.use(axios)
app.mount('#app')
```

---

### **四、服务端实现 (Egg.js)**

#### 1. 创建 Egg 项目
```bash
cd ..
npm init egg --type=ts server
cd server
npm install
npm install xtemplate @types/node cors --save
```

#### 2. 配置跨域 `config/config.default.ts`
```typescript
config.cors = {
  origin: '*',
  allowMethods: 'GET,HEAD,PUT,POST,DELETE,PATCH'
}

config.security = {
  csrf: {
    enable: false
  }
}
```

#### 3. 创建控制器 `app/controller/home.ts`
```typescript
import { Controller } from 'egg'

export default class HomeController extends Controller {
  private storedData: any = null

  // 保存数据
  async save() {
    const { ctx } = this
    this.storedData = ctx.request.body
    ctx.body = { success: true }
  }

  // 渲染HTML
  async render() {
    const { ctx } = this
    if (!this.storedData) ctx.throw(404, '没有数据')
    
    const html = `
      <!DOCTYPE html>
      <html>
        <head>
          <style>
            ${this.processStyles(this.storedData.styles || {})}
          </style>
        </head>
        <body>
          ${this.renderComponents(this.storedData.components)}
        </body>
      </html>
    `
    ctx.body = html
  }

  // 处理组件
  private renderComponents(components: any[]): string {
    return components.map(comp => {
      if (comp.type === 'text') {
        return `<div>${comp.content}</div>`
      }
      return `<div class="${comp.attributes?.class}">${comp.content}</div>`
    }).join('')
  }

  // 处理样式
  private processStyles(styles: Record<string, string>): string {
    return Object.entries(styles)
      .map(([selector, rules]) => `${selector} { ${rules} }`)
      .join('\n')
  }
}
```

#### 4. 配置路由 `app/router.ts`
```typescript
import { Application } from 'egg'

export default (app: Application) => {
  const { controller, router } = app
  router.post('/api/save', controller.home.save)
  router.get('/api/render', controller.home.render)
}
```

---

### **五、Mock 数据模拟**

#### 1. 创建复杂结构 `mock/complex.json`
```json
{
  "components": [
    {
      "type": "text",
      "content": "欢迎使用CMS系统",
      "attributes": {
        "class": "header"
      }
    },
    {
      "type": "div",
      "attributes": {
        "class": "content"
      },
      "components": [
        {
          "type": "text",
          "content": "今日新闻："
        },
        {
          "type": "div",
          "components": [
            {
              "type": "text",
              "content": "1. 重大科技突破"
            },
            {
              "type": "text",
              "content": "2. 国际时事速递"
            }
          ]
        }
      ]
    }
  ],
  "styles": {
    ".header": "font-size: 24px; color: blue;",
    ".content": "padding: 20px; border: 1px solid #ccc;"
  }
}
```

#### 2. 更新控制器加载Mock数据
```typescript
// app/controller/home.ts
import complexData from '../../../mock/complex.json'

// 在 render 方法中使用
async render() {
  const data = complexData // 替换为 this.storedData 用于真实数据
  // ...渲染逻辑
}
```

---

### **六、启动项目**

#### 1. 启动服务端
```bash
cd server
npm run dev
# 访问 http://localhost:7001
```

#### 2. 启动客户端
```bash
cd ../client
npm run dev
# 访问 http://localhost:5173
```

---

### **七、操作流程演示**

1. **编辑界面**  
   ![GrapesJS 编辑界面](https://via.placeholder.com/800x400?text=GrapesJS+Editor)

2. **保存数据**  
   点击保存按钮 → 数据发送到服务端

3. **预览渲染**  
   点击预览按钮 → 显示服务端渲染的HTML

4. **查看结果**  
   ```html
   <div class="header">欢迎使用CMS系统</div>
   <div class="content">
     <div>今日新闻：</div>
     <div>
       <div>1. 重大科技突破</div>
       <div>2. 国际时事速递</div>
     </div>
   </div>
   ```

---

### **八、常见问题解决**

1. **跨域错误**  
   - 确保服务端 `config/config.default.ts` 中的 CORS 配置正确
   - 重启服务端应用

2. **组件不显示**  
   - 检查浏览器控制台是否有错误
   - 确认 GrapesJS 容器元素 ID 正确 (`#editor`)

3. **样式不生效**  
   - 检查服务端 `processStyles` 方法是否正确处理样式
   - 查看生成的 `<style>` 标签内容

---

通过这个完整方案，您可以：
✅ 从零搭建 Vue + Egg 的全栈项目  
✅ 实现可视化编辑与实时预览  
✅ 理解前后端数据交互流程  
✅ 掌握基础的服务端渲染原理
