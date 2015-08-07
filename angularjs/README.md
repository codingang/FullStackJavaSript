##Level-1:

###controller:

```
app.controller('...', function(){
this.product = gem;
});
var gem= {

...
..
}
```

```
div ng-controller="Store....as <alias>
<h1> {{store.product.name}}</h1>
<button ng-show="store.product.canPurchase">Add to cart</button>
ng-hide <----these are directive
<div class="product row" ng-repeat="product in store.products">
```

##Level- 2:

```
{{ data | filter:options }}

ng-src <---for image <img ng-src="{{product.images[0].full}}"/>

ng-init="tab=1"
ng-click
ng-class

value=cur || 0; //cur or 0
this.current=value;
```

##Level - 3:

###ng-model : is for two way binding

```
<!DOCTYPE html>
<html ng-app="gemStore">
  <head>
    <link rel="stylesheet" type="text/css" href="bootstrap.min.css" />
    <script type="text/javascript" src="angular.min.js"></script>
    <script type="text/javascript" src="app.js"></script>
  </head>

  <body ng-controller="StoreController as store">
    <!--  Store Header  -->
    <header>
      <h1 class="text-center">Flatlander Crafted Gems</h1>
      <h2 class="text-center">� an Angular store �</h2>
    </header>

    <!--  Products Container  -->
    <div class="list-group">
      <!--  Product Container  -->
      <div class="list-group-item" ng-repeat="product in store.products">
        <h3>{{product.name}} <em class="pull-right">{{product.price | currency}}</em></h3>

        <!-- Image Gallery  -->
        <div ng-controller="GalleryController as gallery"  ng-show="product.images.length">
          <div class="img-wrap">
            <img ng-src="{{product.images[gallery.current]}}" />
          </div>
          <ul class="img-thumbnails clearfix">
            <li class="small-image pull-left thumbnail" ng-repeat="image in product.images">
              <img ng-src="{{image}}" />
            </li>
          </ul>
        </div>

        <!-- Product Tabs  -->
        <section ng-controller="TabController as tab">
          <ul class="nav nav-pills">
            <li ng-class="{ active:tab.isSet(1) }">
              <a href="" ng-click="tab.setTab(1)">Description</a>
            </li>
            <li ng-class="{ active:tab.isSet(2) }">
              <a href="" ng-click="tab.setTab(2)">Specs</a>
            </li>
            <li ng-class="{ active:tab.isSet(3) }">
              <a href="" ng-click="tab.setTab(3)">Reviews</a>
            </li>
          </ul>

          <!--  Description Tab's Content  -->
          <div ng-show="tab.isSet(1)">
            <h4>Description</h4>
            <blockquote>{{product.description}}</blockquote>
          </div>

          <!--  Spec Tab's Content  -->
          <div ng-show="tab.isSet(2)">
            <h4>Specs</h4>
            <blockquote>Shine: {{product.shine}}</blockquote>
          </div>

          <!--  Review Tab's Content  -->
          <div ng-show="tab.isSet(3)">

            <!--  Product Reviews List -->
            <ul>
              <h4>Reviews</h4>
              <li ng-repeat="review in product.reviews">
                <blockquote>
                  <strong>{{review.stars}} Stars</strong>
                  {{review.body}}
                  <cite class="clearfix">�{{review.author}}</cite>
                </blockquote>
              </li>
            </ul>

            <!--  Review Form -->
            <form name="reviewForm">
              <!--  Live Preview -->
              <blockquote>
                <strong>{{review.stars}} Stars</strong>
                {{review.body}}
                <cite class="clearfix">�{{review.author}}</cite>
              </blockquote>

              <!--  Review Form -->
              <h4>Submit a Review</h4>
              <fieldset class="form-group">
                <select ng-model="review.stars" class="form-control" ng-options="stars for stars in [5,4,3,2,1]"  title="Stars">
                  <option value="">Rate the Product</option>
                </select>
              </fieldset>
              <fieldset class="form-group">
                <textarea ng-model="review.body" class="form-control" placeholder="Write a short review of the product..." title="Review"></textarea>
              </fieldset>
              <fieldset class="form-group">
                <input ng-model="review.author" type="email" class="form-control" placeholder="jimmyDean@example.org" title="Email" />
              </fieldset>
              <fieldset class="form-group">
                <input type="submit" class="btn btn-primary pull-right" value="Submit Review" />
              </fieldset>
            </form>

          </div>

        </section>
      </div>
    </div>
  </body>
</html>
```

###$valid - build in function for validation
###ng-dirty - typing border