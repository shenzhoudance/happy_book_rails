# 老手写代码

## 不要重复

原则：　重复出现了３次的内容，要重构，DRY。

某个controller ：　
```
  def edit
    @article_category = ArticleCategory.find(params[:id])
  end

  def update
    @article_category = ArticleCategory.find(params[:id])
    @article_category.name = params[:article_category][:name]
    @article_category.save
    redirect_to article_categories_path
  end

  def destroy
    @article_category = ArticleCategory.find(params[:id])
    @article_category.delete
    redirect_to article_categories_path
  end
```

要改成：


```
  before_action :get_by_id, :only => [:edit,update, :destroy]

  def edit
  end

  def update
    @article_category.name = params[:article_category][:name]
    @article_category.save
    redirect_to article_categories_path
  end

  def destroy
    @article_category.delete
    redirect_to article_categories_path
  end
  private
  def get_by_id
    @article_category = ArticleCategory.find(params[:id])
  end
```


## 一种搜索页的查询模式：

下面这种页面写起来太恐怖，　重复的太多了：

```
  def show_index
    @categories = ArticleCategory.all
    if params[:title1].present?
      @one_articles = Article.all.joins(:article_category).where(:article_category_id=>@categories[0].id).where("title like '%#{params[:title1]}%'")
    else
      @one_articles = Article.all.joins(:article_category).where(:article_category_id=>@categories[0].id)
    end
  end
```

如何重构呢？　要了解，　rails的查询，是一种查询专用语言。（DSL）。
要理解：

```
Article.where().where().page().per()
```
等同于：

```
Article.page().per().where().where()
```

他们的顺序，不是严格按照出现的顺序执行的。 而是大部分时间，跟顺序无关的。
只有在 调用 `.all`的时候，才会真正查询数据库。

所以，上面的代码，我们要改。

```
  def show_index
    @categories = ArticleCategory.all

    @one_articles = Article.joins(:article_category).where(:article_category_id=>@categories[0].id)
    @one_articles = @one_articles.where("title like '%#{params[:title1]}%'") if params[:title1].present?

  end
```

## 不要节省空间

不要这样写：　
```
{ :x => @x_axis, :y => @daily_pvs }
```


要这样写：　
```
{
  :x => @x_axis,
  :y => @daily_pvs
}
```

## migration comment

要加上注释，　例如：

```
      t.string :title, comment:"文章标题, 例如： 《缓存的使用》"
      t.string :author, comment:"文章作者, 例如： 王同学"
      t.string :content, comment:"文章内容, 例如：<div>lalala</div>"
      t.belongs_to :article_category, comment:"与文章分类表关联"

```
