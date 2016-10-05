在 controller中，  在 create/ update/ 方法的尾部， 我们需要跳转到其他URL。
会看到这样的代码：

```
def create
  book = Book.create params[:book]
  redirect_to book
end
```

上面的 redirect_to book, 等同于：

```
  redirect_to book
  redirect_to book_path(:id => book.id)
  redirect_to '/books/' + book.id
```
