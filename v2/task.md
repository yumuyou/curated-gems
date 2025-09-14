# 第2课任务指南（逐行指引版）

本节课我们将在现有的 `app.js` 基础上做 4 个小改动。  
目标是：**每一步改完 → 保存 → 刷新 → 立刻看到效果**。  
零基础友好，只要按照指引找到函数位置，把代码粘贴进去即可。

---

## 任务 1：改搜索框提示文字（热身）

**目的**：找到 `mountControls()` 函数，敢于改一行字符串。

1. 打开 `app.js`，搜索函数名 `function mountControls()`。
2. 找到里面的 placeholder 定义：

```js
const placeholder = lang === 'zh'
    ? '🔍 输入关键词搜索精彩内容...'
    : '🔍 Enter keywords to search amazing content...';
```

3. 把中文这行改成你自己的风格，例如：

```js
? '👉 想搜点啥？试试看~'
```

4. 保存，刷新网页，你会看到搜索框里出现你刚写的文案。

---

## 任务 2：空结果提示更好玩

**目的**：当没有结果时，显示个性化提示。

1. 打开 `app.js`，搜索 `const emptyTexts`。
2. 你会看到：

```js
const emptyTexts = {
    zh: '😅 没有找到相关内容，换个关键词试试吧',
    en: '😅 No relevant content found, try different keywords'
};
```

3. 把中文这行改成你自己的语气，例如：

```js
zh: '🦄 咦？什么都没搜到，要不要换个词？',
```

4. 保存刷新后，输入一个不存在的词，就能看到你的提示出现。

---

## 任务 3：加一个彩蛋（输入特殊词触发）

**目的**：给大家成就感——只要复制一段代码，输入暗号就能触发效果。

1. 打开 `app.js`，搜索函数名 `function applyAndRender()`。
2. 在函数最后一行 `render(view);` **之后**粘贴：

```js
// 彩蛋：输入 wow 出现礼花
if (query === 'wow') {
  alert('🎉 你发现了彩蛋！');
}
```

3. 保存刷新后，在搜索框里输入 `wow`，就会跳出提示框 🎉。

---

## 任务 4：在数据源标签上显示数量统计

**目的**：在「全部 / 各数据源」标签后显示可见卡片数量，并随搜索实时更新。

### 第一步：在 `applyAndRender()` 里计算数量

1. 打开 `app.js`，搜索函数名 `function applyAndRender()`。
2. 在筛选数据 `view = raw.filter(...)` 之前，粘贴：

```js
// 统计：当前搜索条件下，各数据源可见数量
const counts = { all: 0 };
for (const item of raw) {
  const summaryField = (lang === 'zh' ? item.summary_zh : item.summary_en) || '';
  const quoteField   = (lang === 'zh' ? item.best_quote_zh : item.best_quote_en) || '';
  const titleField   = (lang === 'zh' ? (item.title_zh || item.title) : item.title) || '';
  const tagsArr      = item.tags || [];

  const matchesQuery = !query ||
    titleField.toLowerCase().includes(query) ||
    summaryField.toLowerCase().includes(query) ||
    quoteField.toLowerCase().includes(query) ||
    tagsArr.some(tag => tag.toLowerCase().includes(query));

  if (matchesQuery) {
    counts.all += 1;
    const s = item.source || 'unknown';
    counts[s] = (counts[s] || 0) + 1;
  }
}

// 暴露给 renderSources 使用
window.__countsForCurrentQuery = counts;
```

### 第二步：在 `applyAndRender()` 末尾刷新标签

在 `render(view);` 之前或之后加：

```js
renderSources(['all', ...new Set(raw.map(x => x.source))]);
```

### 第三步：修改 `renderSources(list)`

1. 打开 `app.js`，搜索函数名 `function renderSources(list)`。
2. 在函数开始处加：

```js
const counts = window.__countsForCurrentQuery || { all: raw.length };
```

3. 修改生成标签的地方，把 `displayText` 改成：

```js
const n = counts[source] || 0;
const displayText = source === 'all'
  ? (lang === 'zh'
      ? `📚 全部精选 (${n})`
      : `📚 All Sources (${n})`)
  : `✨ ${source} (${n})`;
```

4. 保持原来的 `isActive` 判断不变。

### 第四步（可选）：数量为 0 时禁用标签

在 return 那行 `<span>` 里追加 `disabled` 类：

```js
const disabled = (counts[source] || 0) === 0 ? 'disabled' : '';
return `<span class="tag ${isActive} ${disabled}" data-source="${source}">${esc(displayText)}</span>`;
```

并在文件`style.css`末尾里加：

```css
.tag.disabled { opacity: .45; pointer-events: none; }
```

---

# 🎉 恭喜！

完成这 4 个任务后，你会发现：  
- 搜索框和提示文案都有了你自己的风格；  
- 空结果时更有趣；  
- 输入 `wow` 会触发彩蛋；  
- 每个数据源标签都会显示当前结果数量。

到此你已经能给小站加上属于你的“魔法”啦 🚀。
