#nest 创建 树tree的工具类
  

1. 构建treeNode

        function treeNode(menuId, menuView, menuAsc, creationTime, modelParent, menuName, constraintPoint, children) {
	    	this.menuId = menuId;
	    	this.menuView = menuView;
	    	this.menuAsc = menuAsc;
	    	this.creationTime = creationTime;
	    	this.modelParent = modelParent;
	    	this.menuName = menuName;
	    	this.constraintPoint = constraintPoint;
	    	this.children = children;
		}
2. 核心代码 

        // modelParent  父级ID
		export function getDFSTree(data, modelParent) {
	    	const treelist = [];
	    	for (let i = 0; i < data.length; i++) {
				//判断父级ID和参数是否一致
	    		if (data[i].modelParent == modelParent) {
	    			const tree = new treeNode(
		    			data[i].menuId,
		    			data[i].menuView,
		    			data[i].menuAsc,
		    			data[i].creationTime,
		    			data[i].modelParent,
		    			data[i].menuName,
		    			data[i].constraintPoint,
		    			getDFSTree(data, data[i].menuId)
					);
	    			treelist.push(tree).toString;
	    		}
	      	}
	   	 	return treelist;
		}



3. //测试数据

	     const data = [
		     { 'id': 1, 'modelParent': 0, 'menuName': '主节点' },
		     { 'id': 2, 'modelParent': 1, 'menuName': '第二层,id2' },
		   	 { 'id': 3, 'modelParent': 1, 'menuName': '第二层,id3' },
		     { 'id': 4, 'modelParent': 3, 'menuName': '第三层,id4' }
	     ];

4. //调用

	     const tree = getDFSTree(data, 0);
	     console.log(tree[0].children)
