顺序从下往上

  <Col span={14} offset={2}>           {            expenseReportType.reportTypeCode === 'QXZBBX' &&            (            <FormItem {...formItemLayout} label="实物资产明细行号">            {getFieldDecorator('entityLineNumber', {             initialValue: [],             rules: [{             required: true ,             validator: this.checkEntityLineNumber            }],            })(<InputNumber             style={{ width: '60%' }}            />)}           </FormItem>           )           }                     </Col>  

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/f42f819cf83046ffb81c64f00c1a8bc5/clipboard.png)

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/c5c0d4e36c384e9ba1e874b16cdced36/clipboard.png)

 //如果跨公司且非事前报账单，只能选可跨公司的费用类型    if(interCompanyFlag){     if(!expenseReportType){      message.warning('保存失败');      this.setState({ saveLoading: false, copyLoading: false, againLoading: false });      return;     }     if(!expenseReportType.isBeforeEvent){      if(!expenseType1){       message.warning('当前费用涉及跨公司且不是事前报账单，费用类型必须可跨公司，请修改');       this.setState({ saveLoading: false, copyLoading: false, againLoading: false });       return;      }      if(!expenseType1.isInterCompany){       message.warning('当前费用涉及跨公司且不是事前报账单，费用类型必须可跨公司，请修改');       this.setState({ saveLoading: false, copyLoading: false, againLoading: false });       return;      }     }    } 

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/a4230e755b044e36b4ce2a2f9cad48cd/clipboard.png)

![img](D:/Youdao/YoudaoNote/findgalaxy@163.com/3f09e1aa9e5c41b99f071466ae752312/clipboard.png)

document-basic-info.js