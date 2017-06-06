# aotoo-validator
aotoo校验模块
1. 支持node与fed端双校验  
2. 纯值校验，与dom无关

## install
```
// install
npm install aotoo-validator --save
```

## instantiation
```
// var regulars = {
//   username: function (val, regu){
//     if (!val) return false
//     return regu.email.test(val) ? 'email' : regu.mobile.test(val) ? 'mobile' : false
//   },
//   password: function (passwd, reg){
//     let val = passwd,level
//     if(val){
//       level = (val.length>5) ? 0 + (val.length>7) + (/[a-z]/.test(val) && /[A-Z]/.test(val)) + (/\d/.test(val) && /\D/.test(val)) + (/\W/.test(val) && /\w/.test(val)) + (val.length > 12) : 0;
//       if(val.length>20||/\s/.test(val)) level=0
//       return level
//     }else{return false}
//   },
//   repassword: function(val){
//     return val ? (val == this.value) : false
//   }
// }

// default regular
var validator = require('aotoo-validator')()

// custrom regular
var validator = require('aotoo-validator')(regulars)
```

## default regulars
```
var block = {
  email    : /^[\.a-zA-Z0-9_=-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$/,
  username : /^[a-zA-Z0-9_\u4e00-\u9fa5]{4,}$/,
  verify   : /^[a-z\d]{4}$/i,
  verify_d : /^[\d]{4}$/i,
  verify_m : /^[\d]{6}$/,
  // password : /^(?=.{5,})(((?=.*[A-Z])(?=.*[a-z]))|((?=.*[A-Z])(?=.*[0-9]))|((?=.*[a-z])(?=.*[0-9]))).*$/,//密码
  password : /^(?![a-zA-z]+$)(?!\d+$)(?![!@#$%^&*]+$)(?![a-zA-z\d]+$)(?![a-zA-z!@#$%^&*]+$)(?![\d!@#$%^&*]+$)[a-zA-Z\d!@#$%^&*]{6,}$/,//密码
  guhua    : /^(\d{3,4})?[-]?\d{7,8}$/,//电话号码的格式
  mobile   : /^(\+?0?86\-?)?1[345789]\d{9}$/, //手机
  url      : /^http[s]?:\/\/([\w-]+\.)+[\w-]+([\w-.\/?%&=]*)?$/, //url
  ip4      : /^(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)\.(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)\.(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)\.(25[0-5]|2[0-4]\d|[0-1]\d{2}|[1-9]?\d)$/, //ip地址
  isEmpty  : isEmpty,
  qq       : /^[1-9]*[1-9][0-9]*$/, //QQ号码
  idcard   : /^(\d{6})(\d{4})(\d{2})(\d{2})(\d{3})([0-9]|X)$/, //身份证
  birthday : /^(\d{4})[\.-](\d{1,2})[\.-](\d{1,2})$/,
  money    : /^[\d]{1,8}(\.\d{1,2})?$/,
  all      : /[\s\S]/,
  tips     : function(msg){ console.log(msg) },
  noop     : function(){ return true },
  isAscii  : function(str){
    assertString(str)
    var ascii = /^[\x00-\x7F]+$/;
    return ascii.test(str);
  },
  isBase64 : function(str){
    assertString(str)
    var notBase64 = /[^A-Z0-9+\/=]/i;
    var len = str.length;
    if (!len || len % 4 !== 0 || notBase64.test(str)) {
      return false;
    }
    var firstPaddingChar = str.indexOf('=');
    return firstPaddingChar === -1 ||
      firstPaddingChar === len - 1 ||
      (firstPaddingChar === len - 2 && str[len - 1] === '=');
  }
}
```

## simple usage
```
/*
  jsonobj: {Json}  custom regulars object
*/
var validator = require('aotoo-validator')(jsonobj)
/*
  default regular verify

  value: {String}  input value
  regular: {String}  default regular
  return: {Boolean} 
*/
var stat = validator(value, regular)()

/*
  ...

  value: {String}  input value
  id: {String}  refer to your input id
  fun: {Function} regular function
  return: {Boolean} 
*/
var stat = validator(value, id, fun)()


/*
  multiple verify

  value: {String}  input value
  id: {String}  refer to your input id
  fun: {Function} regular function
  return: {Boolean} 
*/
var stat = validator(value, id, fun)
           validator(value1, id1, fun1)
           validator(value2, id2, fun2)
           ()

```

## adv usage
```
/*
  multiple verify

  value: {String}  input value
  regular: {String}  default regular
  id: {String}  refer to your input id
  fun: {Function} regular function
  return: {any} 

  query: {Json}  All the correct values will be put into query
  errors: {Array} If any of the checksum values are incorrect, put its id in the errors

  如果任何校验的值不正确，则将它的id放入数组
*/
var stat = validator(value, regular)
           validator(value1, id1, fun1)
           validator(value2, id2, fun2)
           (function(query, errors){
            let errs_arr = []
            if(errs.length !=0 ){
              for(let i=0; i<errs.length; i++){
                errs_arr.push(errs[i].key)
              }
            }
            return errs_arr
           })

```


## sample
```
state2 = valide(data.provinceCode, 'c-province', valideMethod.notEmpty)
               (data.businessModel, 'c-bizModel', valideMethod.notEmpty)
               (data.businessLicenceCode, 'c-number', valideMethod.companyCode)
               ((query, errs)=>{
                 let errs_arr = []
                 if(errs.length !=0 ){
                   for(let i=0; i<errs.length; i++){
                     errs_arr.push(errs[i].key)
                   }
                 }
                 return errs_arr
               })
```
