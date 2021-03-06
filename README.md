# Yii2 component and models for storage uploaded files
## Install by composer
composer require sem-soft/yii2-filestorage
## Or add this code into require section of your composer.json and then call composer update in console
"sem-soft/yii2-filestorage": "*"
## Execute migration
```bash
$ ./yii migrate/up --migrationPath=@vendor/sem-soft/yii2-filestorage/migrations
```
## Usage
In configuration file do
```php
<?php
...
  'components'  =>  [
    ...
    'filestorage'	=>  [
        'class'             => \sem\filestorage\FileStorage::className(),
        'storageBaseUrl'    =>  false,
        'storagePath'       =>  '@webroot',
        'storageDir'        =>  'upload',
        'filemode'          =>  0775 // Если задан, то после создания файла принудительно будет произведена смена прав на указанные
    ]
    ...
  ],
...
 ?>
 ```
Example of Controller action for file uploading
```php
    public function actionIndex()
    {
	$model = new \backend\models\FileForm();
	
	if (Yii::$app->request->isPost) {
	    
            $model->imageFile = UploadedFile::getInstance($model, 'imageFile');
	    
            $file = $model->upload();
	    
        }
	
        return $this->render('index', [
	    'model'	=>  $model
	]);
    }
 ```
 ```php
     public function actionIndex1()
    {
        $model = new \backend\models\FileForm();


            $model->imageFile = new \sem\filestorage\adapters\RemoteFile("https://cs7065.userapi.com/c836722/v836722161/4bff2/mdg7cPZvLrM.jpg");

            $file = $model->upload();

        return $this->render('index', [
                'model' => $model
        ]);
    }
 ```
Example of Upload From Model
```php
<?php

namespace backend\models;

class FileForm extends \yii\base\Model
{
    /**
     * @var UploadedFile
     */
    public $imageFile;

    /**
     * @inheritdoc
     */
    public function rules()
    {
        return [
            [['imageFile'], 'file', 'skipOnEmpty' => false, 'extensions' => 'png, jpg'],
        ];
    }
    
    /**
     * @return boolean
     */
    public function upload()
    {
        if ($this->validate()) {
	    $file = new \sem\filestorage\models\File($this->imageFile,[
		'group_code'	=>  'banners',
		'object_id'	=>  '345',
		'allowedExtensions' =>	[
		    'png',
		    'jpeg',
		    'jpg'
		]
	    ]);
	    if ($file->save()) {
		return $file;
	    }
        }
	
	return false;
    }
}
```
Example of Form View
```php
    <?php $form = ActiveForm::begin(['options' => ['enctype' => 'multipart/form-data']]) ?>

        <?= $form->field($model, 'imageFile')->fileInput() ?>

        <button type="submit">Submit</button>
    <?php ActiveForm::end() ?>
```
Example of displaying files data
```php
    public function actionTest()
    {
	foreach (\sem\filestorage\models\File::find()->all() as $f) {
	    echo $f->getUrl(true) . "<br>";
	    echo $f->url . "<br>";
	    echo $f->name . "<br>";
	    echo $f->path . "<br>";
	    echo $f->size . "<br>";
	    echo \sem\helpers\FileHelper::formatSize($f->size) . "<br>";
	    echo "<br>";
	    echo "<br>";
	}
    }
```
Example of displaying images data and cache
```php
    public function actionTest()
    {
        foreach (\sem\filestorage\models\Image::find()->all() as $f) {
            echo $f->getUrl(true) . "<br>";
            echo $f->url . "<br>";
            echo $f->name . "<br>";
            echo $f->path . "<br>";
            echo $f->size . "<br>";
            echo \sem\helpers\FileHelper::formatSize($f->size) . "<br>";
            echo $f->isImage . "<br>";

            echo "------heighten:<br>";
            $heighten = $f->heighten(300);
            echo $heighten->getUrl(true) . "<br>";
            echo $heighten->url . "<br>";
            echo $heighten->path . "<br>";
            echo \yii\helpers\Html::img($heighten->url);
            echo "<br>";

            echo "------widen:<br>";
            $widen = $f->widen(200);
            echo $widen->getUrl(true) . "<br>";
            echo $widen->url . "<br>";
            echo $widen->path . "<br>";
            echo \yii\helpers\Html::img($widen->url);
            echo "<br>";

            echo "------contain:<br>";
            $contain = $f->contain(100, 120);
            echo $contain->getUrl(true) . "<br>";
            echo $contain->url . "<br>";
            echo $contain->path . "<br>";
            echo \yii\helpers\Html::img($contain->url);
            echo "<br>";

            echo "------cover:<br>";
            $cover = $f->cover(100, 100);
            echo $cover->getUrl(true) . "<br>";
            echo $cover->url . "<br>";
            echo $cover->path . "<br>";
            echo \yii\helpers\Html::img($cover->url);
            echo "<br>";
            echo "<br>";
        }
    }
```
