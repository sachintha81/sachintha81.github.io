---
layout: post
title:  "Passing data between two Forms/Windows in .NET."
date:   2018-08-20 13:22:00 -0700
categories: [c#, wpf, winforms, delegates]
---
<div class="post-categories">
  {% if post %}
    {% assign categories = post.categories %}
  {% else %}
    {% assign categories = page.categories %}
  {% endif %}
  {% for category in categories %}
  <a href="{{site.baseurl}}/categories/#{{category|slugize}}">{{category}}</a>
  {% unless forloop.last %}&nbsp;{% endunless %}
  {% endfor %}
</div>
<br>

Setup
--
You have a `WPF` application (this works just fine in `WinForms` too) with two `Forms`. One, the main window of the application, which we will call `Main` hereafter, and a child window which opens upon clicking a `Button` on the `Main`. We'll call that - you guess it right - `Child`. The `Child` has a `ComboBox` on it, which is populated with a bunch of simple strings. The `Main` has a `Label` (shown in green) on it as well. They look like this:

#### Main Window
<img src="/assets/images/2018-08-20-data-passing/Main.png" alt="Main Window"/>

#### Child Window
<img src="/assets/images/2018-08-20-data-passing/Child.png" alt="Main Window"/>


Objective
--
Select items on the `ComboBox` on `Child`, and have the changes reflected on the `Label` on `Main` in real-time.


Plan Outline
--
We don't want to pass the entire `Main` window to the `Child` (or the other way round) - that's just ugly. We don't want to have a _Messaging Service_, that's a bit of an overkill for a simple task. Instead, we'll make use of <a href="https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/delegates/" target="_blank">delegates</a>. Essentially, a delegate is a reference type variable that holds a reference to a method. Or, to put it into really simple terms, a delegate is sort of a pointer to a method. You _invoke_ the delegate, and the method which the delegate points to gets called. I encourage you to read the linked documentation on delegates, and if you're still confused, ask Google.

So here's what we're going to do. In our `Main`, we'll declare a delegate that points to a method that takes in one `string` variable as the sole parameter. Then we pass the _delegate_ to our `Child` when we instantiate the `Child` window. In the `Child` side of things, whenever we change selection in the `ComboBox`, we grab the selected text, and call the delegate in the `Child` by passing the grabbed text as the parameter. Then the delegate will go ahead and call the method it is pointing to (which, remember, resides on the `Main`). Now, if, inside this all important method, we set the text of the `Label` with the value passed from the `Child`, we should be good.

Does all that sound a little too confusing? Well, don't worry, we'll go ahead with implementing this step-by-step which I promise will make things clear.

Implementation
--
On our `Main` code-behind, let's declare a delegate that takes in one `string` parameter. It should be outside the `class`. Then, within the `class` on a global scope, let's create an instance of it.

{% highlight c# %}
public delegate void DataTransferDelegate(string data);

public partial class MainWindow : Window
{
	public DataTransferDelegate del;

	public MainWindow()
	{
		InitializeComponent();
	}
}
{% endhighlight %}

Then we'll also declare the method we talked about earlier, which should have the same _method signature_ as the delegate, i.e., take in one `string` parameter and the return type is `void`. In the method body, we'd simply set the `Label` text to whatever was passed as the parameter.

{% highlight c# %}
public delegate void DataTransferDelegate(string data);

public partial class MainWindow : Window
{
	public DataTransferDelegate del;

	public MainWindow()
	{
		InitializeComponent();
	}

	public void PassData(string data)
	{
		LblDisplay.Content = data;
	}
}
{% endhighlight %}

Now, how do we _point_ the delegate to this method? Well, it's pretty easy, and we call it _registering_ the method to the delegate.

{% highlight c# %}
    del += new DataTransferDelegate(PassData);
{% endhighlight %}

We'll put a pin in there and let's focus on the `Child` for a moment. We'll return to the `Main` and finish the code, and I will show a complete sample of the code-behind of `Main`.

I mentioned that we need to _pass_ the said delegate to the `Child`, and invoke it when the `ComboBox` selection changes. So let's do that. We'll edit the `Child` constructor and pass a delegate of type `DataTransferDelegate`, which we will assign to a local instance. Then, inside the `ComboBox`'s `SelectionChanged` event, we'll call (or `Invoke` which is the proper term) the delegate.

{% highlight c# %}
public partial class ChildWindow : Window
{
	public DataTransferDelegate dataTransferDelegate;

	public ChildWindow(DataTransferDelegate del)
	{
		InitializeComponent();

		dataTransferDelegate = del;

		InitCombo();
	}

	private void InitCombo()
	{
		CmbItems.Items.Add("Arthur Dent");
		CmbItems.Items.Add("Tricia McMillan");
		CmbItems.Items.Add("Ford Prefect");
		CmbItems.Items.Add("Zaphod Beeblebrox");
		CmbItems.SelectedIndex = 0;
	}

	private void CmbItems_SelectionChanged(object sender, SelectionChangedEventArgs e)
	{
		dataTransferDelegate?.Invoke(CmbItems.SelectedItem.ToString());
	}
}
{% endhighlight %}

Back to the `Main`. Following is the entire code-behind of `Main`. In the button `Click` even we fire up an instance of the `Child` window, and pass our delegate (which, remember again, we've _registered_ with the `PassData()` method) to it. And on the `Child` side, when the selection is changed the delegate gets invoked, which in turn will call this method, and set the `Label` text.

{% highlight c# %}
public delegate void DataTransferDelegate(string data);

public partial class MainWindow : Window
{
	public DataTransferDelegate del;

	public MainWindow()
	{
		InitializeComponent();

		del += new DataTransferDelegate(PassData);
	}

	private void BtnOpenWindow_Click(object sender, RoutedEventArgs e)
	{
		ChildWindow childWindow = new ChildWindow(del);
		childWindow.Show();
	}

	public void PassData(string data)
	{
		LblDisplay.Content = data;
	}
}
{% endhighlight %}

Now evertything's in place and we're done. When the `ComboBox` selection is changed, the changes should be reflected on the `Main` in real-time.

#### Selection change at `Child`
<img src="/assets/images/2018-08-20-data-passing/ChildSelection.png" alt="Child Window - Selection Changed"/>

#### Change is reflected on `Main`
<img src="/assets/images/2018-08-20-data-passing/MainChanged.png" alt="Main Window - Change is Reflected"/>


Download
--
The entire project is available to download <a href="https://github.com/sachintha81/PublicDownloads/blob/master/DataPassing.zip/" target="_blank">here</a>.
