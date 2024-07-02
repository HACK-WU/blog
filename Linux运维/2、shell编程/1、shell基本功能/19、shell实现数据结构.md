# 1、shell 实现面向对象

```
[root@server1 ~]# cat mian.sh
#!/usr/bin/bash

declare -A Array

set -u
set -e

function call {

	function init {
		local prefix=$1
		shift
		set +u
		while [ $# -gt 0  ];
		do
	 		Array["$prefix"_"$1"]=$2
			shift 2
		done
		set -u
		
	}	
	
	function get_name {
		local name=$1_name
		echo ${Array[$name]}
	}
		
	function get_age {
	    local age=$1_age
		echo ${Array[$age]}
	}	
	$*		
}


zhangsan=(
	name zhangsan
	age 28
)

call  init   zhangsan ${zhangsan[*]}
call  get_name  zhangsan
call get_age zhangsan

lisi=(
	name lisi
	age 22
	firend 
)

call  init lisi ${lisi[*]}
call   get_name  lisi
call get_age lisi
```

# 2、shell 实现队列

```
function datastruct {
	local name="张三"
	local age=18		
}

function queue {
	local name="队列的名称"
	local front=0
	local feal=0
	declare -A array
	array[$i]="datastruct str1 str2"
	function push {   #入队
		let feal++	
	    str1=$1
  		str2=$2	
		array[$feal]="datatstrcy $str1 $str2"		
	}
	
	function pop {
		local result= $( arrar[$feal]  )
		echo  resut
	}

}
```