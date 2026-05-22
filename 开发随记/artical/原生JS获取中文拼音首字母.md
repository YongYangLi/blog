## 实现思路
找到所有声母的边界中文字符，分别和目标中文通过localeCompare比较顺序。
例如A的边界中文是“驁”（ào），B的边界中文是“簿”（bù），目标中文“不”。

```javascript
"驁".localeCompare("不", 'zh-CN-u-co-pinyin') >= 0  // false
"簿".localeCompare("不", 'zh-CN-u-co-pinyin') >= 0  // true
// 因此可以判断出“不”的声母为B
```
localeCompare() 方法返回一个数字，表示参考字符串在排序顺序中是在给定字符串之前、之后还是与之相同。第二个参数locales 表示缩写语言代码（BCP 47 language tag）的字符串。参考文章：[MDN - String.prototype.localeCompare()](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/localeCompare)


## 实现代码
```javascript
// 拼音声母可能的首字母
const PINYIN_INITIAL_CONSONANT_LETTERS = 'ABCDEFGHJKLMNOPQRSTWXYZ'.split('');
// 拼音声母对应的边界中文
const PINYIN_BOUNDARY_CHAR = '驁簿錯鵽樲鰒餜靃攟鬠纙鞪黁漚曝裠鶸蜶籜鶩鑂韻糳'.split('');

/**
 * 获取拼音首字母（大写）, 如果不是中文，返回原字符
 * 示例
 *  '中文' => 'ZW'
 *  '中文123' => 'ZW123'
 *  'abc' => 'abc'
 */
function getChinesePinyinAbbreviation(str) {
	// 空字符串直接返回
	if (!str) {
    	return '';
    }
	if (str.length > 1) {
		return str.split('').map(getChinesePinyinAbbreviation).join('');
	}
	// 判断字符是否为中文,不是中文返回原字符
	if (/[^\u4e00-\u9fa5]/.test(str)) {
 		return str;
 	}
	const index = PINYIN_BOUNDARY_CHAR.findIndex((char) => {
		return char.localeCompare(str, 'zh-CN-u-co-pinyin') >= 0;
	});
	return PINYIN_INITIAL_CONSONANT_LETTERS[index];
}
console.log(getChinesePinyinAbbreviation("中国123")) //ZG123
```
