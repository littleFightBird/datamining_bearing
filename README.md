# 轴承故障检测
- 简述
	- DC上的一个训练赛，简单的多分类问题。
- 过程
	- 获取数据集
		- 官方给出了数据集下载地址，直接下载即可。这是一个轴承在一段时间内的振动信号数值及其故障类别。注意，振动信号数值已经说明，时间序列的同列取值不相关，甚至可以认为每一个id时间序列数目也不一样，所以必须对时序数据提取特征。
		- 注意，数据集仓库还有其他一些高校数据集，注意版权问题。
	- 数据探索
		- 简单查看数据特征
			- ![](https://img-blog.csdnimg.cn/20190331155534471.png)
		- 不难看出，还是比较规范的数据集。
			- 训练集有792条记录，每个记录有6002列，第一列为轴承id，最后一列为故障类别（label），中间的6000列为振动信号时序数据。
			- 测试集类似训练集，但是没有最后一列label，需要通过算法获得分类结果。
			- 结果集为测试集id加上对应label即可。
	- 数据预处理
		- 数值规范化
			- 由于是机器记录的振动信号值，无需也不能做标准化的数据变动处理。（当然，数据集无0值等，就算有，也不应该修改，因为这是真实数据。当然，机器记录错误除外。）
		- 时序数据特征提取
			- 既然时序数据之间没有对比度（错相位），必然要对每个记录的时序数据进行特征提取。
	- 数据挖掘建模
		- 第一步就是选择提取时序数据特征的方法。（注意，赛题提到了数据不对等，不可以将每个时序数据当做特征，时序数据可能是错相位的。）
			- 首先选择了比较简单提取一部分我认为合理的时序数据特征值如方差、偏度等，然后根据构造的由新特征组成的数据
			- 之后尝试了使用离散傅里叶变换进行特征提取
		- 模型的选择
			- 工程中使用的是KNN、非线性多分类SVM、随机森林，在傅里叶变换特征下准确率均达到100%。
- 部分代码
	- ```python
		# 定义函数计算时序数据特征值，参考tsfresh，但是减少了特征量
		import numpy as np
		from scipy import stats
		def get_t(df):
		    mean_list = []
		    std_list = []
		    var_list = []
		    min_list = []
		    max_list = []
		    median_list = []
		    skew_list = []
		    kuri_list = []
		    x = pd.DataFrame()
		    for i in range(len(df)):
		        mean_list.append(np.mean(df.iloc[i][1:].values))
		        std_list.append(np.std(df.iloc[i][1:].values))
		        var_list.append(np.var(df.iloc[i][1:].values))
		        min_list.append(np.min(df.iloc[i][1:].values))
		        max_list.append(np.max(df.iloc[i][1:].values))
		        median_list.append(np.median(df.iloc[i][1:].values))
		        skew_list.append(stats.skew(df.iloc[i][1:].values))
		        kuri_list.append(stats.kurtosis(df.iloc[i][1:].values))
		    x['mean'] = mean_list
		    x['std'] = std_list
		    x['var'] = var_list
		    x['min'] = min_list
		    x['max'] = max_list
		    x['median'] = median_list
		    x['skew'] = skew_list
		    x['kuri'] = kuri_list
		    return x
		```
- 补充说明
	- 使用开发工具为Jupyter Notebook，对于大数据领域的不二选择，方便边记录、边思考、边编码，而且不需要反复运行同样的代码。
	- 具体数据集和代码见我的Github，欢迎Star或者Fork。（切换分支branch查看）
