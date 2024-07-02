VS2010C++ADO连接SQLServer（亲测可用）



kuai- 2020-11-20 11:47:34  225  收藏 5

文章标签： sql

版权

一、ADO相关知识

详细介绍可参考：ADO编程详解

介绍的十分详细。



二、可用代码

以下代码亲测可直接使用，功能为输出一张表里的所有记录。

有两个需要自行修改的地方：



## 1.修改服务器名等信息

```cpp
pMyConnect->Open("Provider=SQLOLEDB; Server=.;Database=JXGL; uid=sa; pwd=17386111245","","",adModeUnknown);           
	} //SQLServ验证登陆
//windows身份验证HRESULT hr = m_pConnection->Open("Provider=SQLOLEDB;Data Source=.;Initial Catalog=Market;Integrated Security=SSPI;","","",-1);
```

2.修改嵌入的SQL语句

```javascript
pRst = pMyConnect->Execute("select * from STUDENT",NULL,adCmdText);
1
```

将Execute中的SQL语句改为你需要的，即可。

3.完整代码

```cpp
// ado.cpp : 定义控制台应用程序的入口点。
//

#include "stdafx.h"
#include<iostream>
#include<string>
#include<vector>
using namespace std;
#import "C:/Program Files/Common Files/System/ado/msado15.dll" no_namespace rename("EOF","adoEOF")

int _tmain(int argc, _TCHAR* argv[])
{
	CoInitialize(NULL); //初始化COM环境           
	_ConnectionPtr pMyConnect(__uuidof(Connection));//定义连接对象并实例化对象 
	_RecordsetPtr pRst(__uuidof(Recordset));//定义记录集对象并实例化对象               
	try           
	{              
		//步骤2：创建数据源连接
		/*打开数据库“SQLServer”，这里需要根据自己PC的数据库的情况 */             
		pMyConnect->Open("Provider=SQLOLEDB; Server=.;Database=JXGL; uid=sa; pwd=17386111245","","",adModeUnknown);           
	} 
	catch (_com_error &e)           
	{               
		 cout<<"Initiate failed!"<<endl;               
		 cout<<e.Description()<<endl;               
		 cout<<e.HelpFile()<<endl;               
		 return 0;           
	}           
	cout<<"Connect succeed!"<<endl;      



	
	 
	//步骤3：对数据源中的数据库/表进行操作
	try           
	{
		pRst = pMyConnect->Execute("select * from STUDENT",NULL,adCmdText);//执行SQL： select * from STUDENT         
		if(!pRst->BOF) 
		{
			pRst->MoveFirst(); 
		}               
		else
		{                    
			cout<<"Data is empty!"<<endl;                     
			return 0;                
		}               
		vector<_bstr_t> column_name;      
		 
		/*存储表的所有列名，显示表的列名*/             
		for(int i=0; i< pRst->Fields->GetCount();i++)               
		{                    
			cout<<pRst->Fields->GetItem(_variant_t((long)i))->Name<<" ";                    
			column_name.push_back(pRst->Fields->GetItem(_variant_t((long)i))->Name);               
		}   
		cout<<endl;
		 
		/*对表进行遍历访问,显示表中每一行的内容*/           
		while(!pRst->adoEOF)               
		{                    
			vector<_bstr_t>::iterator iter=column_name.begin();                    
			for(iter;iter!=column_name.end();iter++)                    
			{                         
				if(pRst->GetCollect(*iter).vt !=VT_NULL)                         
				{  
					cout<<(_bstr_t)pRst->GetCollect(*iter)<<" ";                         
				}                         
				else
				{
					cout<<"NULL"<<endl;  
				}                  
			}
			pRst->MoveNext();                   
			cout<<endl;              
		}           
	}



	catch(_com_error &e)           
	{               
		cout<<e.Description()<<endl;               
		cout<<e.HelpFile()<<endl;               
		return 0;          
	}  

	
 
	//步骤4：关闭数据源
	/*关闭数据库并释放指针*/        
	try           
	{               
		pRst->Close();     //关闭记录集               
		pMyConnect->Close();//关闭数据库               
		pRst.Release();//释放记录集对象指针               
		pMyConnect.Release();//释放连接对象指针
	}
	catch(_com_error &e)           
	{               
		cout<<e.Description()<<endl;               
		cout<<e.HelpFile()<<endl;               
		return 0;           
	}                  
	CoUninitialize(); //释放COM环境
	return 0;

	

	return 0;
}
```

