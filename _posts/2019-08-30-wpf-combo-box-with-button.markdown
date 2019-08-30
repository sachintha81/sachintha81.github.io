---
layout: post
title:  "WPF ComboBox with Button for select and add"
date:   2019-08-30 13:48:00 -0700
categories: [c#, wpf, combobox, button]
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

`ComboBox` controls are great for selecting an item from a list of items; that's literally what they're there for. But what if you want to select an item, add it to somewhere, select another item, and add it again. There are few approaches you can take.

- On `SelectionChanged` event handler, add to a list. Perhaps not the most user-friendly option.
- Multi-select `ComboBox`, and each item has a `CheckBox` control to select/deselect. Okay, this is pretty neat.

But I want to take a different approach. I simply want to select an item old style, and then click a `Button` to add it to a list. This is actually pretty easy; on the `Click` event handler of the `Button` simply retrieve the `SelectedItem` of the `ComboBox` and add the value to a list. This is all good, but the problem is that the two controls stand alone. So I want to bundle them together and have a custom control, and we'll use a `UserControl` for this purpose. I want it to look something like this:

<img src="/assets/images/2019-08-30-wpf-combo-box-with-button/combobutton.jpg" alt="Buttoned ComboBox Concept"/>

So my `UserControl` would have two controls; a `ComboBox` and a `Button`. I want to be able to bind something from the 'main' (parent) to the `UserControl`. Then upon selecting something and clicking the `Button`, I want the `UserControl` to notify to the parent of the event occurrence, and also pass the selected value.

The `UserControl`'s `XAML`

{% highlight xml %}
<UserControl ...
             d:DesignHeight="40" d:DesignWidth="200">
    <Grid>
        <Grid.ColumnDefinitions>
            <ColumnDefinition Width="160"/>
            <ColumnDefinition Width="40"/>
        </Grid.ColumnDefinitions>

        <ComboBox Grid.Column="0" Margin="4" Name="ItemsComboBox"
                  ItemsSource="{Binding Source, RelativeSource={RelativeSource AncestorType=UserControl}}"/>
        <Button Grid.Column="1" Margin="4" Content="+"
                Click="Button_Click"/>
    </Grid>
</UserControl>
{% endhighlight %}

The following binding will allow me to bind a list of data in the parent to the `ComboBox` in my `UserControl`.

{% highlight xml %}
<Grid>
    <local:UCComboButton Grid.Row="0" Width="200" Height="40" x:Name="MyUC"
                         Source="{Binding Names}"/>
</Grid>
{% endhighlight %}

And in the `UserControl`s code behind:

{% highlight c# %}
public partial class UCComboButton : UserControl
{
    public UCComboButton()
    {
        InitializeComponent();
    }

    // We use this dependency property to bind a list to the combo box.
    public static readonly DependencyProperty SourceProperty = DependencyProperty.Register("Source", typeof(IEnumerable), typeof(UCComboButton), new PropertyMetadata(null));

    public IEnumerable Source
    {
        get { return (IEnumerable)GetValue(SourceProperty); }
        set { SetValue(SourceProperty, value); }
    }

    // This is to send the occurred event, in this case button click, to the parent, along with the selected data.
    public class SelectedItemEventArgs : EventArgs
    {
        public string SelectedChoice { get; set; }
    }

    public event EventHandler<SelectedItemEventArgs> ItemHasBeenSelected;

    private void Button_Click(object sender, RoutedEventArgs e)
    {
        var selected = ItemsComboBox.SelectedValue;
        ItemHasBeenSelected?.Invoke(this, new SelectedItemEventArgs { SelectedChoice = selected.ToString() });
    }
}
{% endhighlight %}

Now in the `MainWindow.xaml.cs`:

{% highlight c# %}
public MainWindow()
{
    InitializeComponent();
    // Subscribe to the item selected event
    MyUC.ItemHasBeenSelected += UCButtonClicked;

    Names = new List<string>
    {
        "A",
        "B",
        "C"
    };

    DataContext = this;
}

void UCButtonClicked(object sender, UCComboButton.SelectedItemEventArgs e)
{
    var value = e.SelectedChoice;
    // Do something with the value
}
{% endhighlight %}

Note that the above `Names` list is what's bound to the `UserControl` from the main window `XAML`.

