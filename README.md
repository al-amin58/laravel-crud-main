# Laravel Crud With Multiple Image



### Creating Product

```php

  use Illuminate\Support\Facades\Request;
  use App\Models\Product;
  use App\Models\Image;

  public function store()
    {

        $data = Request::validate([[
            'title' => 'required',
            'price' => 'required',
            'description' => 'required',
            'images' => 'required'
        ]);
        $product = Product::create($data);
        $images = $data['images'];

        $imageData = [];
        foreach( $images as $image){
            $imageData[] = [
                'url' => '/storage/'.$image['file']->store('uploads', 'public'),
                'product_id' => $product->id
            ];
        }
        Image::insert($imageData);
        return response()->json([
          'status' => 'success',
          'message' => 'Product Added Successfully'
        ]);
    }
```

### Delete Single Image

```php
  use Illuminate\Support\Facades\Storage;
  use App\Models\Image;

    public function deleteImage($id) {
        $image = Image::find($id);
        $imagePath = str_replace('/storage', 'public', $image->URL);

        Storage::delete($imagePath);
        $image->delete();

        return response()->json([
          'status' => 'success',
          'message' => 'Image Deleted Successfully'
        ]);
    }
```


### Update Product

```php

  use Illuminate\Support\Facades\Request;
  use App\Models\Product;
  use App\Models\Image;

  public function update($id) {
        $product = Product::find($id);

        $data = Request::validate([[
            'title' => 'required',
            'price' => 'required',
            'description' => 'required',
            'newImage' => 'required'
        ]);
        $images = $data['newImage'];
        $imageData = [];

        foreach( $images as $image){
            $imageData[] = [
                'url' => '/storage/'.$image['file']->store('uploads', 'public'),
                'product_id' => $product->id
            ];
        }
        Image::insert($imageData);
        $product->update($data);

        return response()->json([
          'status' => 'success',
          'message' => 'Product Updated Successfully'
        ]);
    }
```

### Delete Product

```php
  use Illuminate\Support\Facades\Storage;
  use App\Models\Product;

  public function destroy($id){
        $product = Product::find($id);
        if ($product) {
            $images = $product->images;
            foreach ($images as $image) {
                $imagePath = str_replace('/storage', 'public', $image->url);
                Storage::delete($imagePath);
                $image->delete();
            }
            $product->delete();
        }
    }
```
