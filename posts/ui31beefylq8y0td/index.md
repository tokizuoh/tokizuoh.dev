---
title: "UICollectionViewCellの読み込みの高速化を考える ③先読み編"
date: 2021-11-27T17:17:32+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- swift
---
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 13.1
Build version 13A1030d
```
  
## はじめに
何かお題を作って、それに対しどのようにアプローチしていくかを記事として残す。  
  
今回は[前回](https://tokizuoh.dev/posts/ad1wj87xryo7n1wa/)の課題になっていた、一回目の表示をスムーズにするために先読みを実装する。  
  
## 先読み
  
```swift
import UIKit

final class LoadImagesViewController: ComponentBaseViewController {

    @IBOutlet weak var collectionView: UICollectionView! {
        didSet {
            collectionView.dataSource = self
            collectionView.register(R.nib.loadImagesCollectionViewCell)
            configureFlowLayout()
        }
    }

    struct ViewControllerModel {
        let thumbnailImages: [UIImage]
    }

    private var viewControllerModel: ViewControllerModel?
    var presenter: LoadImagesPresenter!
    private let cellCount = 300

    override func viewDidLoad() {
        super.viewDidLoad()
        configureNavigationItem(navigationTitle: "018 LoadImages")
        collectionView.isHidden = true
        presenter.getImages()
    }

}

extension LoadImagesViewController: UICollectionViewDataSource {

    func collectionView(_ collectionView: UICollectionView, numberOfItemsInSection section: Int) -> Int {
        return cellCount
    }

    func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell {
        guard let viewControllerModel = viewControllerModel else {
            return UICollectionViewCell()
        }

        let cell = collectionView.dequeueReusableCell(withReuseIdentifier: R.reuseIdentifier.loadImagesCollectionViewCell,
                                                      for: indexPath)!
        let index = indexPath.row % viewControllerModel.thumbnailImages.count
        let image = viewControllerModel.thumbnailImages[index]
        cell.setup(image: image)
        return cell
    }

    private func configureFlowLayout() {
        let layout = UICollectionViewFlowLayout()
        layout.itemSize = .init(width: collectionView.frame.width,
                                height: collectionView.frame.height)
        layout.scrollDirection = .horizontal
        collectionView.collectionViewLayout = layout
    }

}

extension LoadImagesViewController: LoadImagesPresenterOutput {

    func updateViewControllerModel(_ viewControllerModel: ViewControllerModel) {
        self.viewControllerModel = viewControllerModel
        collectionView.isHidden = false
        collectionView.reloadData()
    }

}
```
  
　
  
```swift
import UIKit

protocol LoadImagesPresenter {
    init(output: LoadImagesPresenterOutput)
    func getImages()
}

final class LoadImagesPresenterImplement: LoadImagesPresenter {

    private weak var output: LoadImagesPresenterOutput?
    private var imagesCacher: [URL: UIImage] = [:]
    private let urls: [URL] = [
        URL(string: "https://placehold.jp/7276c4/ffffff/1000x2000.png?text=1000%20%C3%97%202000")!,
        URL(string: "https://placehold.jp/a4b562/ffffff/1000x2000.png?text=1000%20%C3%97%202000")!,
        URL(string: "https://placehold.jp/b56262/ffffff/1000x2000.png?text=1000%20%C3%97%202000")!,
        URL(string: "https://placehold.jp/b262b5/ffffff/1000x2000.png?text=1000%20%C3%97%202000")!,
        URL(string: "https://placehold.jp/6297b5/ffffff/1000x2000.png?text=1000%20%C3%97%202000")!,
        URL(string: "https://raw.githubusercontent.com/tokizuoh/Pendula/feature/%23104/Pendula/View/Component/018_LoadImages/Image/sky.jpeg")!
    ]

    init(output: LoadImagesPresenterOutput) {
        self.output = output
    }

    func getImages() {
        let images: [UIImage] = urls.compactMap {
            return getImage(url: $0)
        }

        output?.updateViewControllerModel(.init(thumbnailImages: images))
    }

    private func getImage(url: URL) -> UIImage? {
        if let image = imagesCacher[url] {
            return image

        } else {
            let image = fetchImage(url: url)
            imagesCacher[url] = image
            return image
        }
    }

    private func fetchImage(url: URL) -> UIImage? {
        guard let data = try? Data(contentsOf: url) else {
            return nil
        }

        return .init(data: data)
    }

}

protocol LoadImagesPresenterOutput: AnyObject {
    func updateViewControllerModel(_ viewControllerModel: LoadImagesViewController.ViewControllerModel)
}

```
  
今回からは処理の責務分割のためにMVPを導入。  
画像を読み込み終えてから UICollectionView を表示するようにした。  
  
## gif
  
![](./1.gif)
  
左: before, 右: after
  
　
  
人力キャプチャのため、正確な比較はできないが、  
  
- 左は空の画像のfetchに時間がかかっているため、空の画像読み込み前のスクロールがスムーズにできていない
- 右は画像を全て取得してから UICollectionView を表示しているため、スクロールがスムーズにできている
  
## おわりに  
  
3記事でステップを踏んで UICollectionViewCell の読み込みの高速化を考えた。  
  
サードパーティの画像読み込みライブラリ（Nukeなど）を使う必要性を感じないのでこれから調べる。  
  
## 本記事のシリーズ
  
- [①基本実装編](https://tokizuoh.dev/posts/ip4pe036kfiwusjh/)
- [②キャッシュ編](https://tokizuoh.dev/posts/ad1wj87xryo7n1wa/)
- ③先読み編 (本記事)
  