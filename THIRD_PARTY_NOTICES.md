# Third-party notices

The MetisCloud APEX application loads the following client-side libraries at
runtime. Their upstream licenses apply to those components; the MetisCloud
license applies to MetisCloud itself.

| Component | Version | License | Upstream |
| --- | --- | --- | --- |
| Tabulator | 6.5.2 | MIT | [Project license](https://github.com/tabulator-tables/tabulator/blob/master/LICENSE) |
| Apache ECharts | 6.1.0 | Apache-2.0 | [Project license](https://github.com/apache/echarts/blob/master/LICENSE) |
| jsPDF | 4.2.1 | MIT | [Project repository](https://github.com/parallax/jsPDF) |
| DOMPurify | 3.4.14 | MPL-2.0 OR Apache-2.0 | [Package metadata](https://github.com/cure53/DOMPurify/blob/main/package.json) |
| Marked | 18.0.11 | MIT | [Project repository](https://github.com/markedjs/marked) |
| QR Code Generator | 2.0.4 | MIT | [Project license](https://github.com/kazuhikoarase/qrcode-generator/blob/master/LICENSE) |
| Google Material Symbols | Hosted | Apache-2.0 | [Project documentation and license](https://github.com/google/material-design-icons/blob/master/README.md#license) |
| PrismJS | 1.30.0 | MIT | [Versioned project license](https://github.com/PrismJS/prism/blob/v1.30.0/LICENSE) |
| ExcelJS | 4.4.0 | MIT | [Versioned project license](https://github.com/exceljs/exceljs/blob/v4.4.0/LICENSE) |

The application export references or embeds these libraries. In particular,
PrismJS is redistributed as APEX application static files inside the release
bundle, not only as an external runtime reference.

ExcelJS 4.4.0 supports Excel and PivotTable downloads and is loaded from the
pinned CDN dependency. Copyright (c) 2014-2019 Guyon Roche. Its upstream MIT
license requires retaining its copyright and permission notice with copies or
substantial portions of the library. Retain the upstream license if vendoring it.

## PrismJS 1.30.0

The bundled assets are `prism.js`, `prism.min.js`, `prism-dark.css`,
`prism-dark.min.css`, `prism-light.css`, and `prism-light.min.css`.
The theme files include MetisCloud styling adaptations. The following upstream
copyright and MIT license apply to the PrismJS code and themes, including their
minified and adapted copies. Retain this notice when redistributing those assets.

```text
MIT LICENSE

Copyright (c) 2012 Lea Verou

Permission is hereby granted, free of charge, to any person obtaining a copy
of this software and associated documentation files (the "Software"), to deal
in the Software without restriction, including without limitation the rights
to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
copies of the Software, and to permit persons to whom the Software is
furnished to do so, subject to the following conditions:
The above copyright notice and this permission notice shall be included in
all copies or substantial portions of the Software.
THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
THE SOFTWARE.
```
