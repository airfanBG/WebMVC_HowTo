
# Partial views in ASP.NET Core

A partial view is a [Razor](xref:mvc/views/razor) markup file (`.cshtml`) without an [`@page`](xref:mvc/views/razor#page) directive that renders HTML output *within* another markup file's rendered output.

:::moniker range=">= aspnetcore-2.1"

The term *partial view* is used when developing either an MVC app, where markup files are called *views*, or a Razor Pages app, where markup files are called *pages*. This topic generically refers to MVC views and Razor Pages pages as *markup files*.

:::moniker-end


## When to use partial views

Partial views are an effective way to:

* Break up large markup files into smaller components.

  In a large, complex markup file composed of several logical pieces, there's an advantage to working with each piece isolated into a partial view. The code in the markup file is manageable because the markup only contains the overall page structure and references to partial views.
* Reduce the duplication of common markup content across markup files.

  When the same markup elements are used across markup files, a partial view removes the duplication of markup content into one partial view file. When the markup is changed in the partial view, it updates the rendered output of the markup files that use the partial view.

Partial views shouldn't be used to maintain common layout elements. Common layout elements should be specified in [_Layout.cshtml](layout.md) files.

Don't use a partial view where complex rendering logic or code execution is required to render the markup. Instead of a partial view, use a [view component](view-components.md).

## Declare partial views

:::moniker range=">= aspnetcore-2.0"

A partial view is a `.cshtml` markup file without an [`@page`](razor.md#page) directive maintained within the *Views* folder (MVC) or *Pages* folder (Razor Pages).

In ASP.NET Core MVC, a controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view. In Razor Pages, a <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> can return a partial view represented as a <xref:Microsoft.AspNetCore.Mvc.PartialViewResult> object. Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.

Unlike MVC view or page rendering, a partial view doesn't run `_ViewStart.cshtml`. For more information on `_ViewStart.cshtml`, see <xref:mvc/views/layout>.

Partial view file names often begin with an underscore (`_`). This naming convention isn't required, but it helps to visually differentiate partial views from views and pages.

:::moniker-end

:::moniker range="< aspnetcore-2.0"

A partial view is a `.cshtml` markup file maintained within the *Views* folder.

A controller's <xref:Microsoft.AspNetCore.Mvc.ViewResult> is capable of returning either a view or a partial view. Referencing and rendering partial views is described in the [Reference a partial view](#reference-a-partial-view) section.

Unlike MVC view rendering, a partial view doesn't run `_ViewStart.cshtml`. For more information on `_ViewStart.cshtml`.

Partial view file names often begin with an underscore (`_`). This naming convention isn't required, but it helps to visually differentiate partial views from views.

:::moniker-end

## Reference a partial view

:::moniker range=">= aspnetcore-2.0"

### Use a partial view in a Razor Pages PageModel

In ASP.NET Core 2.0 or 2.1, the following handler method renders the *\_AuthorPartialRP.cshtml* partial view to the response:

```csharp
public IActionResult OnGetPartial() =>
    new PartialViewResult
    {
        ViewName = "_AuthorPartialRP",
        ViewData = ViewData,
    };
```

### Use a partial view in a markup file

:::moniker range=">= aspnetcore-2.1"

Within a markup file, there are several ways to reference a partial view. We recommend that apps use one of the following asynchronous rendering approaches:

* [Partial Tag Helper](#partial-tag-helper)
* [Asynchronous HTML Helper](#asynchronous-html-helper)

:::moniker-end

:::moniker range="< aspnetcore-2.1"

Within a markup file, there are two ways to reference a partial view:

* [Asynchronous HTML Helper](#asynchronous-html-helper)
* [Synchronous HTML Helper](#synchronous-html-helper)

We recommend that apps use the [Asynchronous HTML Helper](#asynchronous-html-helper).

:::moniker-end

:::moniker range=">= aspnetcore-2.1"

### Partial Tag Helper

The Partial Tag Helper renders content asynchronously and uses an HTML-like syntax:

```cshtml
<partial name="_PartialName" />
```

When a file extension is present, the Tag Helper references a partial view that must be in the same folder as the markup file calling the partial view:

```cshtml
<partial name="_PartialName.cshtml" />
```

The following example references a partial view from the app root. Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:

**Razor Pages**

```cshtml
<partial name="~/Pages/Folder/_PartialName.cshtml" />
<partial name="/Pages/Folder/_PartialName.cshtml" />
```

**MVC**

```cshtml
<partial name="~/Views/Folder/_PartialName.cshtml" />
<partial name="/Views/Folder/_PartialName.cshtml" />
```

The following example references a partial view with a relative path:

```cshtml
<partial name="../Account/_PartialName.cshtml" />
```

For more information, see <xref:mvc/views/tag-helpers/builtin-th/partial-tag-helper>.

:::moniker-end

### Asynchronous HTML Helper

When using an HTML Helper, the best practice is to use <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.PartialAsync%2A>. `PartialAsync` returns an <xref:Microsoft.AspNetCore.Html.IHtmlContent> type wrapped in a <xref:System.Threading.Tasks.Task%601>. The method is referenced by prefixing the awaited call with an `@` character:

```cshtml
@await Html.PartialAsync("_PartialName")
```

When the file extension is present, the HTML Helper references a partial view that must be in the same folder as the markup file calling the partial view:

```cshtml
@await Html.PartialAsync("_PartialName.cshtml")
```

The following example references a partial view from the app root. Paths that start with a tilde-slash (`~/`) or a slash (`/`) refer to the app root:

:::moniker range=">= aspnetcore-2.1"

**Razor Pages**

```cshtml
@await Html.PartialAsync("~/Pages/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Pages/Folder/_PartialName.cshtml")
```

**MVC**

:::moniker-end

```cshtml
@await Html.PartialAsync("~/Views/Folder/_PartialName.cshtml")
@await Html.PartialAsync("/Views/Folder/_PartialName.cshtml")
```

The following example references a partial view with a relative path:

```cshtml
@await Html.PartialAsync("../Account/_LoginPartial.cshtml")
```

Alternatively, you can render a partial view with <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartialAsync%2A>. This method doesn't return an <xref:Microsoft.AspNetCore.Html.IHtmlContent>. It streams the rendered output directly to the response. Because the method doesn't return a result, it must be called within a Razor code block.

Since `RenderPartialAsync` streams rendered content, it provides better performance in some scenarios. In performance-critical situations, benchmark the page using both approaches and use the approach that generates a faster response.

### Synchronous HTML Helper

<xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.Partial%2A> and <xref:Microsoft.AspNetCore.Mvc.Rendering.HtmlHelperPartialExtensions.RenderPartial%2A> are the synchronous equivalents of `PartialAsync` and `RenderPartialAsync`, respectively. The synchronous equivalents aren't recommended because there are scenarios in which they deadlock. The synchronous methods are targeted for removal in a future release.

> [!IMPORTANT]
> If you need to execute code, use a [view component](view-components.md) instead of a partial view.

:::moniker range=">= aspnetcore-2.1"

Calling `Partial` or `RenderPartial` results in a Visual Studio analyzer warning. For example, the presence of `Partial` yields the following warning message:

> Use of IHtmlHelper.Partial may result in application deadlocks. Consider using &lt;partial&gt; Tag Helper or IHtmlHelper.PartialAsync.

:::moniker-end

## Partial view discovery

When a partial view is referenced by name without a file extension, the following locations are searched in the stated order:

:::moniker range=">= aspnetcore-2.1"

**Razor Pages**

1. Currently executing page's folder
1. Directory graph above the page's folder
1. `/Shared`
1. `/Pages/Shared`
1. `/Views/Shared`

**MVC**

:::moniker-end

:::moniker range=">= aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`
1. `/Pages/Shared`

:::moniker-end

:::moniker range="< aspnetcore-2.0"

1. `/Areas/<Area-Name>/Views/<Controller-Name>`
1. `/Areas/<Area-Name>/Views/Shared`
1. `/Views/Shared`

:::moniker-end

The following conventions apply to partial view discovery:

* Different partial views with the same file name are allowed when the partial views are in different folders.
* When referencing a partial view by name without a file extension and the partial view is present in both the caller's folder and the *Shared* folder, the partial view in the caller's folder supplies the partial view. If the partial view isn't present in the caller's folder, the partial view is provided from the *Shared* folder. Partial views in the *Shared* folder are called *shared partial views* or *default partial views*.
* Partial views can be *chained*&mdash;a partial view can call another partial view if a circular reference isn't formed by the calls. Relative paths are always relative to the current file, not to the root or parent of the file.

> [!NOTE]
> A [Razor](razor.md) `section` defined in a partial view is invisible to parent markup files. The `section` is only visible to the partial view in which it's defined.

## Access data from partial views

When a partial view is instantiated, it receives a *copy* of the parent's `ViewData` dictionary. Updates made to the data within the partial view aren't persisted to the parent view. `ViewData` changes in a partial view are lost when the partial view returns.

The following example demonstrates how to pass an instance of <xref:Microsoft.AspNetCore.Mvc.ViewFeatures.ViewDataDictionary> to a partial view:

```cshtml
@await Html.PartialAsync("_PartialName", customViewData)
```

You can pass a model into a partial view. The model can be a custom object. You can pass a model with `PartialAsync` (renders a block of content to the caller) or `RenderPartialAsync` (streams the content to the output):

```cshtml
@await Html.PartialAsync("_PartialName", model)
```

:::moniker range=">= aspnetcore-2.1"
