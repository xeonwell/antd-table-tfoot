# antd-table-tfoot
add a tfoot summary for antd table component

## demo step
- init a create-react-app first
```bash
npx create-react-app ant-demo
cd ant-demo
yarn add antd
npm start

```
- drag AntdTableTFoot.js & css into the src dir
- replace app.js with following codes
```js
import React, {Component} from 'react';
import './App.css';
import 'antd/dist/antd.css';
import {Table, Divider, Tag} from 'antd';
import AntdTableTFoot from './AntdTableTFoot';

const columns = [{
  title:     'Name',
  dataIndex: 'name',
  key:       'name',
  render:    text => <a href="javascript:;">{text}</a>,
}, {
  title:     'Age',
  dataIndex: 'age',
  key:       'age',
}, {
  title:     'Address',
  dataIndex: 'address',
  key:       'address',
}, {
  title:     'Tags',
  key:       'tags',
  dataIndex: 'tags',
  render:    tags => (
    <span>
      {tags.map(tag => {
        let color = tag.length > 5 ? 'geekblue' : 'green';
        if (tag === 'loser') {
          color = 'volcano';
        }
        return <Tag color={color} key={tag}>{tag.toUpperCase()}</Tag>;
      })}
    </span>
  ),
}, {
  title:  'Action',
  key:    'action',
  render: (text, record) => (
    <span>
      <a href="javascript:;">Invite {record.name}</a>
      <Divider type="vertical"/>
      <a href="javascript:;">Delete</a>
    </span>
  ),
}];

const data = [{
  key:     '1',
  name:    'John Brown',
  age:     32,
  address: 'New York No. 1 Lake Park',
  tags:    ['nice', 'developer'],
}, {
  key:     '2',
  name:    'Jim Green',
  age:     42,
  address: 'London No. 1 Lake Park',
  tags:    ['loser'],
}, {
  key:     '3',
  name:    'Joe Black',
  age:     32,
  address: 'Sidney No. 1 Lake Park',
  tags:    ['cool', 'teacher'],
}];

const summaryColumns = [
  {dataIndex: 'AverageAge'},
  undefined,
  {
    dataIndex: 'Sum',
    render:    (val) => `<b>￥${val}</b>`,
  },
];

const summaryDataSource = {
  AverageAge: 27.8,
  Sum:        '300012',
};


class App extends Component {

  tableId = `table_${+new Date()}_${parseInt(Math.random() * 100000)}`;

  render() {
    return (
      <div className="App">
        <Table id={this.tableId} columns={columns} dataSource={data} size={'small'}/>
        <AntdTableTFoot tableId={this.tableId}
                        dataSource={data}
                        columns={columns}
                        summaryColumns={summaryColumns}
                        summaryDataSource={summaryDataSource}/>
      </div>
    );
  }
}

export default App;


```
- visit http://localhost:3000

### Tips
currently we use dom operation(innerHTML) to handle this, so the render function must return a DOM string, such as
```js
{
  dataIndex: 'aaa', 
  render:    (val) => `<b>￥${val}</b>`
}
```

if you want the function returns a jsx format, using the ReactDom instead. 

```js
import ReactDom from 'react-dom';
// ...
    // unmount component when remove
    ReactDom.unmountComponentAtNode(tfoot);
    tfoot.parentNode.removeChild(tfoot);
// ...

    // create a tr component and render to the node tFoot 
    const tr = <tr>
      {
        this.props.columns.map((item, idx) => {
          let result;
          if (idx === 0) {
            result = this.props.totalSummaryText;
          } else {
            let summary = this.props.summaryColumns[idx - 1];
            if (summary) {
              result = summary.render ? summary.render(this.props[summary.dataIndex])
                : this.props[summary.dataIndex];
            } else {
              result = ' ';
            }
          }
          return <td key={idx}>
            {result}
          </td>;
        })
      }
    </tr>;

    table.appendChild(tFoot);
    ReactDom.render(tr, tFoot);

// ...
```