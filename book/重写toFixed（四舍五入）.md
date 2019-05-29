```
/**
 * 修复科学计数和法问题
 * @param num 小数
 * @returns {string}
 */
function scientificToNumber(num) {
  const str = num;
  const reg = /^([-]?\d+\.?\d*)(e)([-|+]?\d+)$/;
  let zero = '';
  if (!reg.test(str)) {
    return num;
  }
  const arr = reg.exec(str);
  const len = Math.abs(arr[3]) - 1;
  for (let i = 0; i < len; i += 1) {
    zero += '0';
  }
  if (arr[1].indexOf('-') === 0) {
    return `-0.${zero}${arr[1].replace('-', '').replace('.', '')}`;
  }
  return `0.${zero}${arr[1].replace('.', '')}`;
}

/**
 * 修复toFixed不准确问题
 * @param digit 小数位数
 * @returns {string}
 */
Number.prototype.toFixed = function (digit = 0) {
  let value = `${this}`;
  if (this > 0) {
    value = (Math.round((this * (10 ** digit)) + 0.0001) / (10 ** digit)).toString();
  } else if (this < 0) {
    value = (Math.round((this * (10 ** digit)) - 0.0001) / (10 ** digit)).toString();
  }
  value = scientificToNumber(value);
  const [integer, decimal = ''] = value.split('.');
  if (digit > 0) {
    return `${integer}.${decimal.padEnd(digit, '0')}`;
  }
  return integer;
};

```