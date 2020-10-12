* 参考https://www.cnblogs.com/vvjiang/p/8579046.html 
* Mocha Mocha是流行的JavaScript测试框架之一，通过它添加和运行测试，从而保证代码质量  
    ```
        /**
         * 加法函数
         * @param {第一个数} a
         * @param {第二个数} b
         */
        function addNum(a,b){
            return a+b;
        }
        module.exports=addNum;
        
        
        // 测试代码
        var addNum=require('../src/index')
        
        describe('测试index.js', function() {
          describe('测试addNum函数', function() {
            it('两数相加结果为两个数字的和', function() {
               if(addNum(1,2)!==3){
                 throw new Error("两数相加结果不为两个数字的和")；
               }
            });
          });
        });
    ```
		
    测试脚本里面应该包括一个或多个describe块，每个describe块应该包括一个或多个it块。
    describe块称为"测试套件"（test suite），表示一组相关的测试。它是一个函数，第一个参数是测试套件的名称（"测试index.js"），第二个参数是一个实际执行的函数。
    it块称为"测试用例"（test case），表示一个单独的测试，是测试的最小单位。它也是一个函数，第一个参数是测试用例的名称（"两数相加结果为两个数字的和"），第二个参数是一个实际执行的函数。
		

* Chai 断言  
    在上面的Mocha例子中，测试失败用抛异常来处理，多少有点繁琐，所以就有了断言库的出现。
    这里我们介绍一个常用的断言库chai。
    可以简单理解为这就是对我们上面抛异常方法的一个封装，当判断失败时会抛出一个异常。
    ```jsx harmony
    var expect = require('chai').expect;
    var addNum=require('../src/index')
    
    describe('测试index.js', function() {
      describe('测试addNum函数', function() {
        it('两数相加结果为两个数字的和', function() {
          expect(addNum(1,2)).to.be.equal(3);
        });
      });
    });
    ```


* Enzyme, 在使用Enzyme 前需要先适配React对应的版本
	```
    import {assert} from 'chai'
    import React from 'react'
    import Enzyme from 'enzyme'
    import Adapter from 'enzyme-adapter-react-16'
    import Example from '../src/example'
    
    const {shallow}=Enzyme
    
    Enzyme.configure({ adapter: new Adapter() })
    
    describe('Enzyme的浅渲染测试套件', function () {
      it('Example组件中按钮的名字为text的值', function () {
        const name='按钮名'
        let app = shallow(<Example text={name} />)
        assert.equal(app.find('button').text(),name)
      })
    })
    ```
	
* Enzyme 的使用之mount
    mount方法用于将React组件加载为真实DOM节点。然而真实DOM需要一个浏览器环境，为了解决这个问题，我们可以用到jsdom
    然后在test目录下新建一个文件setup.js
	```jsx harmony
    import jsdom from 'jsdom';
    const { JSDOM } = jsdom;
    
    if (typeof document === 'undefined') {
        const dom=new JSDOM('<!doctype html><html><head></head><body></body></html>');
        global.window =dom.window;
        global.document = global.window.document;
        global.navigator = global.window.navigator;
    }
    
    "scripts": { "test": "mocha --require babel-core/register --require ./test/setup.js" }
    ```
	shallow果然最快，这是肯定的，但是因为shallow的局限性，我们可能更想知道render和mount的效率。
    事实证明，render的效率是mount的两倍。
    有人可能要质疑你为什么不将次数弄更大一点，因为在设定为1000次的情况下mount直接超时了，也就是超过了mocha的默认执行时间限定2000ms。
    那么问题来了，mount存在的价值是什么，render就可以测试子组件，render还不需要jsdom和额外的配置。
    当然是有价值的，shallow和mount因为都是dom对象的缘故，所以都是可以模拟交互的,而render是不能的
		
		
* Sinon
    在讲Sinon之前，我们得先讲一下在学习了Mocha、chai以及enzyme之后，我们的前端测试还存在的一些问题。
    比如前台测试需要与后台交互，获取后台数据后再根据相应数据进行测试。
    又比如一个函数测试依赖另一个函数，我们可以根据测试的目的去模拟另一个函数，讲两者的测试分开，从而达到测试中也能解耦的目的。
    Sinon是用来辅助我们进行前端测试的，在我们的代码需要与其他系统或者函数对接时，它可以模拟这些场景，从而使我们测试的时候不再依赖这些场景。
    Sinon有主要有三个方法辅助我们进行测试：spy，stub，mock。还有fake XHR（模拟xhr请求）、fack server（模拟服务器）以及fake timer（模拟定时器）等操作。这里就不多讲了，具体的可以查看此API：Sinon v4.1.6。
		
		
* Jest 集成了前面所讲的Mocha和chai，jsdom，sinon等功能  
    将jest的运行范围限定在test文件夹下，而不是全部，所以在package.json中加入如下配置：  
    
		"jest": {
		    "testRegex": "/test/.*.test.jsx?$"
		 }
		import React from 'react'
		import { shallow } from 'enzyme'
		import CommentItem from './commentItem'
		
		describe('测试评论列表项组件', () => {
		  // 这是mocha的玩法，jest可以直接兼容
		  it('测试评论内容小于等于200时不出现展开收起按钮', () => {
		    const propsData = {
		      date: '2018-03-04 10:10:11',
		      name: '匿名人',
		      content: '测试标题测试标题测试标题测试标题测试标题测试标题测试标题测试标题测试标题测试标题测试标题测试标题'
		    }
		    const item = shallow(<CommentItem {...propsData} />)
		    //看到这里的断言了吗，实际上和chai的expect是很像的
		    expect(item.find('.btn-expand').length).toBe(0);
		  })
		  // 这是jest的玩法，推荐用这种
		  test('两数相加结果为两个数字的和', () => {
		    expect(3).toBe(3);
		  });
		}
		○ 如果看了上面的代码会发现我没有引用任何类似于
		
		import *  from 'jest'
		的代码，而那个expect是没有定义的。
		这段代码直接运行jest命令没有任何问题，但是eslint会检测出错，对于这种情况，我们可以再eslint配置文件.eslintrc中加入以下代码：
		
		"env": {
		    "jest": true
		  },
		
		○ expect({a:1}).toBe({a:1})//判断两个对象是否相等
		expect(1).not.toBe(2)//判断不等
		
		expect(n).toBeNull();//判断是否为null
		expect(n).toBeUndefined();//判断是否为undefined
		expect(n).toBeDefined();//判断结果与toBeUndefined相反
		expect(n).toBeTruthy();//判断结果为true
		expect(n).toBeFalsy();//判断结果为false
		
		expect(value).toBeGreaterThan(3);//大于3
		expect(value).toBeGreaterThanOrEqual(3.5);//大于等于3.5
		expect(value).toBeLessThan(5);//小于5
		expect(value).toBeLessThanOrEqual(4.5);//小于等于4.5
		
		expect(value).toBeCloseTo(0.3); // 浮点数判断相等
		
		expect('Christoph').toMatch(/stop/);//正则表达式判断
		
		expect(['one','two']).toContain('one');//不解释
		
		
		function compileAndroidCode() {
		  throw new ConfigError('you are using the wrong JDK');
		}
		
		test('compiling android goes as expected', () => {
		  expect(compileAndroidCode).toThrow();
		  expect(compileAndroidCode).toThrow(ConfigError);//判断抛出异常
		}）
		
		○ mock文件和css module的问题
		如果js文件中引用了css或者本地其他文件，那么就可能测试失败。
		为了解决这个问题，同时也为了提高测试效率，：
		
		"jest": {
		    "moduleNameMapper": {
		     "\\.(jpg|jpeg|png|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$": "<rootDir>/test/config/fileMock.js",
		     "\\.(css|less)$": "identity-obj-proxy"
		 }
		而fileMock.js文件内容为：
		
		module.exports = 'test-file-stub';
		然后安装identity-obj-proxy即可：
		
		npm install --save-dev identity-obj-proxy
		
		
		○ jest与别名
		在webpack中经常会用到别名，而jest测试时，如果文件中引用了别名会出现找不到文件的问题。
		
		毕竟jest测试时没有经过webpack处理
		
		对于以下玩法
		
		resolve: {  
		    alias: {  
		        common: path.resolve(__dirname, 'plugins/common/')  
		    }  
		} 
		可以通过
		
		"jest": {
		    "testRegex": "./src/test/.*.test.js$",
		    "moduleNameMapper": {
		      "^common(.*)$": "<rootDir>/plugins/common$1",
		    }
		}
		这个和之前 mock文件和css module的问题 一样，都是使用了moduleNameMapper这个属性
		
		○ 生成测试报告 只需要在jest命令后加入 --coverage即可 jest --coverage
		
e2e测试 nightwatch、f2etest、jest、selenium-webdriver(直接打开浏览器操作测试、需要下载驱动放到醒目根目录下,参考6-QA)
