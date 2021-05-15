<!--
 * @Author: 六弦
 * @LastEditors: 六弦
 * @Date: 2021-05-15 17:57:13
 * @LastEditTime: 2021-05-15 18:11:46
 * @FilePath: /tuya-check-server/Users/liuxian/codeAll/pub/tukong/test.md
-->


实现一个parse函数，传入一个如下所示的json，返回如下的静态代码；
```js
    import parse from  './parse';
    const json = {
        type: 'form',
        config: {
            btn: [{
                type: 'submit',
                text: '提交'
            }]
        },
        children: [{
            type: 'input',
            label: '昵称',
            field: 'nickname',
            config: {
                type: 'normal',
                defaultValue: '嘻嘻',
                require: true
            }
        }, {
            type: 'input',
            label: '密码',
            field: 'password',
                config: {
                type: 'ps',
            }
        }]
    }
    parse(json);
    // 输出如下：
    `
        import { Form, Input, Button } from 'UIComponent';

        export default class extends React.Component {
            state={
                formValue: {
                    nickname: '嘻嘻'
                    password: ''
                }
            }
            changeFormValue = (key) => (value) => {
                this.setState({
                    formValue: {
                        ...formValue,
                        [key]: value
                    }
                }
            }
            submit = () => {
                const {formValue} = this.state;
                // TODO sth
            }
            render(){
                const { formValue } = this.state;
                   return (
                        <Form>
                            <Input type="normal" require label="昵称" key="nickname" value={formValue.nickname} onChange={this.changeFormValue('nickname')}/>
                            <Input type="ps"  label="密码" key="password" value={formValue.password} onChange={this.changeFormValue('password')}/>
                            <div className='btn-group'>
                                <Button onClick={this.submit}>提交</Button>
                            </div>
                        </Form>
                    )
                }
         
        }
        
    `

```