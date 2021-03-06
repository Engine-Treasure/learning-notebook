## XSLT - Extensible Stylesheet Language Transformations

- XML 被设计成
    - 内容与表现分离
    - 计算机程序间数据交换的格式
    - 人类可读写
- 使用 XSLT 转换 XML
    1. 将 XML 数据转成树结构
    2. 数据结构转换：输入树结构 -> 输出树结构
    3. 格式化数据
- 转换过程是基于模板规则的, a template rule = template pattern + template body
- An XSL processors `parses` an XML source and tries to find a `matching template rule`. If it does, instructions inside matching template are `evaluated`.
- Processing always starts with the template match="/" . This instruction means: process all children of the current node, including text nodes.
- 如果一个节点的模板存在，不会调用默认的处理。如果要包含后代节点，需要显式地请求它们的模板
- `|` 匹配多个节点，`*` 匹配所有节点
- `//` - 选择文档中所有指定类型的节点
- 使用模板的 `mode`，可以对一个元素处理多次
- 使用模板的 `priority`，设置优先级
- `@` 获得属性，属性也是 XML 的一个节点，处理与普通节点一样
- `tagName[@attribute]` 选择具有某属性的节点，`tagName[not(@attribute)]` 选择不具有某属性的节点
- `call-template` - 调用定义的模板
- `轴(axis)` - 很重要，"孩子，后代，父亲，祖先，兄弟。。。"
- XSL 样式表本身也是一个 XML 文档，因此它总是由 XML 声明起始
- XSLT 是描述性的
- 基础处理范例是模式匹配。模板的规则不会列出命令性的动作序列，只定义了如何处理匹配到的节点
- `先处理，再输出`
- 用树的结构来表示比 DOM （Document Object Models）高效得多
- XSLT 使用 XPath 在 XML 文档中进行导航。
- 在转换过程中，XSLT 使用 XPath 来定义源文档中可匹配一个或多个预定义模板的部分。一旦匹配被找到，XSLT 就会把源文档的匹配部分转换为结果文档。
- XSLT 的设计目的之一就是使一种格式到另一种格式的转换成为可能
- XSLT 的设计目标之一是使数据在服务器上从一种格式转换到另一种格式成为可能，并向所有类型的浏览器返回可读的数据。
- 当 XML 文件没有包含对 XSL 文件的引用时，其可使用多个不同的 XSL 样式表来进行转换。
- `<xsl:template>` 元素用于构建模板。`match` 属性用于关俩 XML 元素和模板。
    - `<xsl:template match="/">` - <xsl:template> 元素定义了一个模板。而 match="/" 属性则把此模板与 XML 源文档的根相联系。
- `<xsl:value-of>` 元素用于提取某个选定节点的值，并把值添加到转换的输出流中。`select` 属性的值是一个 XPath 表达式. (`此模式，元素的内容被使用之后，将不能进行更多的处理`)
- `<xsl:apply-templates>` - 此模式，将对选择的元素进行更多的处理
- `<xsl:for-each> `元素可用于选取指定的节点集中的每个 XML 元素。也有 `select` 属性。在 `<xsl:for-each>` 元素中添加一个选择属性的判别式，可以过滤从 XML 文件输出和的结果。操作符：`=`, `!=`, `&lt;`, `&gt;`
    - `<xsl:for-each select="catalog/cd[artist='Bob Dylan']">`
- `<xsl:sort>` 元素用于对结果进行排序。
- `<xsl:if>` 元素用于放置针对 XML 文件内容的条件测试。必选的 `test` 属性的值包含了需要求值的表达式。
- `<xsl:choose>` 元素用于结合 <xsl:when> 和 <xsl:otherwise> 来表达多重条件测试。
- `<xsl:attribute>` 向元素添加属性

![XSL Elements](xsl_elements.png)

## XPath

- XPath 使用路径表达式来选取 XML 文档中的节点或者节点集
- 在 XPath 中，有七种类型的节点：元素、`属性`、文本、命名空间、处理指令、注释以及文档节点（或称为根节点）。属性也是节点
- 基本值是无父无子的节点。

![XPath Expression](xpath_expression.png)

- `谓语`用来查找某个特定的节点或者包含某个指定的节点，嵌在方括号`[]`中
- `*` - 匹配任意元素节点
- `@*` - 匹配任意属性节点
- `node()` - 匹配任意类型的节点
- 通过在路径表达式中使用`|`运算符，可以选取若干个路径。
- 轴(`axis`) - 可定义相对于当前节点的节点集。

!(XPath axis)[xpath_axis.png]

- 步(`step`) -  `轴名称::节点测试[谓语]`

