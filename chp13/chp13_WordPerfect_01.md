#第十三章 Word Perfect
-----
<center>![](./img/chap13_01.jpg)</center>        
在前面的章节中，我们掌握了一些测试和操作文本的最基本和最重要的函数。现在我们准备要做的是，分析出现在文本文档中实实在在的文字。关于互联网的一些最基本的函数，像关键字搜索，通过分析“内容”，例如文体中的文字，来完成的。

本章开头的照片是所谓的“文字云”，它是通过测试在推特搜索中输入术语“数据科学”返回所有的文字生成的。（这个网页应用在[http://www.jasondavies.com](http://www.jasondavies.com "http://www.jasondavies.com")）。这些五颜六色的文字看起来挺有趣的，但是它们其实还隐含着一些有用的信息。图片上文字的几何排列一部分是随机的，一部分是为吸引眼球而精心设计的。文字的颜色也是。然而，字体的大小则是这个文字在这张众多文字组成的库中重要性的衡量。Corpus，拉丁语当中是“身体”的意思，它通常是文字分析者参考文本材料的文字，一般包含一个或多个文件。当我们考虑大量的文本数据，一组文档可以使任何东西：网页，你电脑上的文字处理文档，一组推文，或是政府报告。大多数情况下，文字分析者考虑文档集，每个文档集包含一些自然语言文本，这些自然语言文本就会被看做是一个语料库，如果他们打算集中分析所有的文档。

前面的“文字云”表明了“数据”和“科学”两个术语在推特的搜索结果当中显得非常重要，但搜索结果中还有其他无数的，看起来似乎不那么重要的，却也同样令人生趣的文字。我们看到“算法”，“分子”，“结构”，“调查”这些词，它们在数据科学的上下文中非常有用。我们还看到其他的术语，像“基督教徒”，“促进”和“协调员”这些看起来似乎和原搜索术语“数据科学”没有明显关联的词语。“文字云”这个小例子向我们展示了自然语言处理和搜索的相关领域的一项基本的挑战：确保文本分析产生的结果是和用户脑中的任务相关联的。

在本章我们将用到一些新的R包来扩展我们处理文本和从推特得到的数据中建立我们自己的文字云的能力。如果你还没有阅读上一章节“字符串原理”，那么你应该在继续本章前先去阅读的，因为我们在那里建立了一些技巧。

无论你阅读完上一章的那个地方，你需要取得并预处理一个推文的数据集，用一些你已经写好的代码，或者新的。在上一章的结尾，我们提供了一些TweetFrame()函数的样本代码，这个函数利用一个查找术语和一个最大的推文限制，然后返回一个按时间排序的包含推文的数据帧。虽然函数中注释很多，但真正的只有三行功能代码用到了twitteR包的功能，并且为我们从推特上得到了数据。下面的活动中，我们仍然利用上一章我们通过这一命令得到的数据帧进行工作：
>\>tweetDF <- TweetFrame("#solar",100)

这就是数据帧，tweetDF,包含100条带"#solar"标签的推文，可推测出大多数关于太阳能和“绿色”相关的主题。在我们利用这两个新R包工作之前，我们可以通过丢弃大量对文字云没有任何意义的垃圾来改善显示质量。为了完成这个目的，我们调用了其他函数来去除多余的空格和所有的URL字符串，还去除了重复的推文标题，删除标签，消除了指向其他人的推文句柄。所有的这些变形，我们都采用了上一章中提到的字符串包中的字符串替换函数。举个例子，思考下下面这个口令，出现在CleanTweet()函数的倒数第二行：
>\>tweets <- str_replace_all(tweets, "@[a-z,A-Z]*","")  

你应该觉得这行代码很容易理解，否则，你应该做多一点练习。左边很简单：我们用赋值箭头把右边表达式的结果赋给“tweets”这个数据对象。请注意，当这个语句在这章末尾处的函数中被执行时，“tweets”只是个临时数据对象变量，它在CleanTweets()中使用之后就会自动消失。

右边表达式用了stringr包中的str_replace_all()这个函数。我们使用"all"这个函数而不使用str_replace()，原因是我们希望每个推文可以进行多重匹配。str_replace_all()这个函数有3个参数。第一个是输入，它是一个字符串矢量(我们用"tweets"这个临时数据变量作为文本资源同时也作为它的目标输出)，第二个是要匹配的正则表达式，第三个是要替换匹配的字符串，其实是个空字符串，双引号中间什么也没有。本例中的正则表达式以“@”符号开头，后面跟着0个或多个小写或大写字母。最后的星号"*"就是代表0个或多个的意思。这个正则表达式会匹配推文中的任何相匹配的账户名字。

如果你看过一些推文，你会发现人们在发推文时经常相互@大家的推特账户名字，例如，@SolarFred时不时地就出现在推文文本当中。你可以自己做一些调查：用户名可以同时包含数字和字母吗？如果可以，你应该怎样修改上面的正则表达式，使它能够把0-9这些数字也成为用户名的一部分呢？另外，我们为什么要把用户名从推文内容中去除？如果我们没有去除它们，文字云会是个什么样子的呢？


无论你是键入本章末尾处的函数还是打算一条一条口令单独的输入，让我们获得一个在原数据帧之外的文本分离矢量开始吧：
>\>cleanText <- tweetDF$text  
>\>head(cleanText, 10)

做这个的唯一目的是简化展示的剩余部分。你可以很容易拷贝tweetDF$text数据到同一个数据帧的另一列，如果你想的话。我们把这个练习独立起来是因为我们不需要担心围绕数据帧的剩余部分的信息。上面的head()口令给你返回一个你开始工作的预览。现在让我们运行我们自定义的cleaning函数吧：
>\>cleanText <- CleanTweets(cleanText)
>\>head(cleanText, 10)

既然上面第一行我们用“cleanText”这个数据对象作为参数输入和目标输出。这是一个老式的计算机科学技巧，它是为了减少用到的临时变量的个数。这样，它恰恰会完成我们想要的，首先执行右边的表达式：把"cleanText"作为参数输入到CleanTweets()函数中运行，然后把返回的结果也赋给"cleanText"这个变量，覆盖了它原来的内容。请记住，我们允许对“cleanText”这个变量的内容做任何操作，因为它只是原数据的一个副本，而且我们的原数据是原封不动的（例如，tweetDF数据帧中的文本列）。

head()函数应该返回一个简短的被过滤了大量无关垃圾的推文列表。如果你跟着这些 步骤，“cleanText”就是一个为下面的工作做准备的字符串矢量(在这个例子中，刚好是100个字符串)。我们现在用"tm"包去处理我们的文本。本例中的"tm"是指“文本挖掘”，它是R提供的众多的文本分析包中比较受欢迎的一个包。另外，文本挖掘时是指从文本内容中解剖有用的可分析的信息的操作（corpora是corpus的复数）。虽然有些人认为文本挖掘和自然语言处理可以交替使用，但它们还是有些细微的差别需要我们考虑的。首先，文本挖掘的“挖掘”部分是指从大数据集中寻找意想不到的模式这种行为的领域，或者是某些人认为的从数据库中发现知识。相反地，自然语言处理更关注的是机器为什么能够通过编程来理解（或机器自己学习）或怎样识别人类语言，使其有意义。同样地，文本挖掘通常关注的是分析文本数据的统计学函数，使用像统计文本内容中文字频次的策略。在自然语言处理中，我们听到更多的是根据语言学，我们把文本按语法分成一个一个部分，例如名词和动词。在R中的“tm”包，我们可以十分肯定它是属于统计学的阵营，它主要是把文本内容分成一个文字序列，然后统计我们找到的不同的文字和序列。

首先，确定你安装了“tm”包并导入了R的副本和R-studio库内。你可以R-studio中的图形接口或者我们前一章写的EnsurePackage()函数。一旦tm包已经准备就绪，你就可以执行以下口令了：

>\>tweetCorpus <- Corpus(VectorSource(cleanText))   
>\>tweetCorpus    
> A corpus with 100 text documents   
> \>tweetCorpus <- tm_map(tweetCorpus, tolower)   
> \>tweetCorpus <- tm_map(tweetCorpus, removePunctuation)   
> \>tweetCorpus <- tm_map(tweetCorpus, removeWords, + stopwords('english'))

