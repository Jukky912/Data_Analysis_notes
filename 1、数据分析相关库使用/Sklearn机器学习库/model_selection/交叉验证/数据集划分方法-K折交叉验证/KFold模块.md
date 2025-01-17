# KFold模块

from sklearn.cross_validation import KFold

## K折交叉验证原理

这便是交叉验证的过程:

    1、首先随机地将数据集切分为 k 个互不相交的大小相同的子集；

    2、然后将 k-1 个子集当成训练集训练模型，剩下的 (held out) 一个子集当测试集测试模型；

    3、将上一步对可能的 k 种选择重复进行 (每次挑一个不同的子集做测试集)；

    4、在每个训练集上训练后得到一个模型,用这个模型在相应的测试集上测试，计算并保存模型的评估指标 (通过K次训练得到模型的平均误差)，

    5、这样就训练了 k 个模型，每个模型都在相应的测试集上计算测试误差，得到了 k 个测试误差。
       

__1、对这 k 次的测试误差取平均便得到一个交叉验证误差,并作为当前 k 折交叉验证下模型的性能指标。__

__2、在模型选择时，假设模型有许多可以调整的参数可供调参，一组可以调整的参数便确定一个模型，计算其交叉验证误差，最后选择使得交叉验证误差最小的那一组的调整参数。这便是模型选择过程。__ 

__3、简而言之，就是我们通过交叉验证验证不同的模型，或者不同的参数组合，最终我们选择准确度高的作为我们的模型。__

k 一般大于等于2，实际操作时一般从3开始取，只有在原始数据集样本数量小的时候才会尝试取2。

__k折交叉验证可以有效的避免过拟合以及欠拟合状态的发生，最后得到的结果也比较具有说服性。__


## Kfold交叉验证实际上做的事情

把训练集分为3份，使用其中的(k-1)份作为训练集，剩下的1份作为交叉验证集

    1. A、B作训练集，C作测试集；
    2. A、C作训练集，B作测试集；
    3. B、C作训练集，A作测试集；

然后三组测试结果取平均。__最后通过最后的平均误差，来评估这个模型__

值得注意的是：交叉验证重点在于 __验证__ 不建模,如果我们想要使用交叉验证得到最优的模型参数可以借助 __网格搜索(GirdSearchCV)__

### 小结 Kfold 交叉验证流程

首先，__交叉验证是用来估计预测误差的__，从而进行模型选择，这一点与AIC、BIC本质没有区别。但是AIC、BIC等准则是从统计理论中推导出来的，而交叉验证是完完全全基于给定的样本。

以Kfold为例,假设有n个观测值，我们将其均分为K组。用其中K-1组来训练模型，然后用训练得到的模型对剩下的一组进行预测，并在该组上计算预测误差。因为从K组中选择K-1组有K种选择，也可以理解为这K个组都有可能成为剩下的那一个预测组。所以便会计算K次的预测误差，__对这K次的预测误差平均便得到一个交叉验证误差。__

以上过程便称为K-fold交叉验证。在模型选择时，假设模型序列有个tuning parameter，不同的tuning parameter便确定一个模型，计算其交叉验证误差，最后选择使得交叉验证误差最小的那一个tuning parameter。这便是模型选择过程。


## k折交叉验证最大的优点：

    所有数据都会参与到训练和预测中，有效避免过拟合，充分体现了交叉的思想
    
交叉验证可能存在 bias 或者 variance。如果我们提高切分的数量 k，variance 会上升但 bias 可能会下降。相反得，如果降低 k，bias 可能会上升但 variance 会下降。bias-variance tradeoff 是一个有趣的问题，我们希望模型的 bias 和 variance 都很低，但有时候做不到，只好权衡利弊，选取他们二者的平衡点。

通常使用10折交叉验证，当然这也取决于训练数据的样本数量。

## KFold模块使用

__函数使用__

    KFold(n_splits=3, shuffle=False, random_state=None)

__参数介绍__
    
    n_splits：表示划分几等份，表示划分为几组，案例中n_splits=2，也就是被划成2组，两组TRAIN和TEST，而不是两个index
    
    shuffle：在每次划分时，是否进行洗牌
        
        ①若为Falses时，其效果等同于random_state等于整数，每次划分的结果相同

        ②若为True时，每次划分的结果都不一样，表示经过洗牌，随机取样的
        
    random_state：随机种子数，不同的种子会造成不同的随机采样结果。相同的种子采样结果相同。

__方法__

    .get_n_splits([X，y，群组])         返回交叉验证器中的拆分迭代次数
    .split(X [，y，groups])             生成索引以将数据拆分为训练和测试集。
    
__案例__
    
    from sklearn.model_selection import KFold

    X = ["a", "b", "c", "d"]
    kf = KFold(n_splits=2)
    print(kf.get_n_splits(X))    # 输出 2

    for train_index,test_index in kf.split(X):
        print("TRAIN:", train_index, "TEST:", test_index)
        
    # TRAIN: [2 3] TEST: [0 1]
    # TRAIN: [0 1] TEST: [2 3]
    


