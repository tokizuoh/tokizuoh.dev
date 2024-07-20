---
title: "UITableViewCellの並び替えアニメーションを試す"
date: 2021-03-07T10:57:04+09:00
draft: false
thumbnail: "https://tokizuoh.dev/images/thumbnail/swift.png"
tags:
- Swift
---
  
本コードはiOS14.0以上で動作します。  
  
<!--more-->  
  
## 開発環境  
  
```bash
> xcodebuild -version
Xcode 12.3
Build version 12C33
```
  
## gif  
  
![](./1.gif)  
  
## コード  
  
```swift
import UIKit

private enum Section: Hashable {
    case one
}

private struct Item: Hashable {
    let title: String?
    
    init(title: String?) {
        self.title = title
    }
    
    private let identifier = UUID()
    
    static var oneItem: [Item] = [
        Item(title: "0"),
        Item(title: "1"),
        Item(title: "2"),
        Item(title: "3"),
        Item(title: "4"),
        Item(title: "5"),
        Item(title: "6"),
    ]
}

final class ViewController: UIViewController {
    
    @IBOutlet weak var tableView: UITableView!
    private var dataSource: UITableViewDiffableDataSource<Section, Item>!
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        tableView.tableFooterView = UIView(frame: .zero)
        configureTableViewDataSource()
    }
    
    private func configureTableViewDataSource() {
        dataSource = UITableViewDiffableDataSource<Section, Item>(tableView: tableView, cellProvider: { (tableView, indexPath, item) -> UITableViewCell? in
            let cell = UITableViewCell()
            var contentConfiguration = cell.defaultContentConfiguration()
            contentConfiguration.text = item.title
            cell.contentConfiguration = contentConfiguration
            return cell
        })
        
        var snapshot = NSDiffableDataSourceSnapshot<Section, Item>()
        snapshot.appendSections([.one])
        snapshot.appendItems(Item.oneItem, toSection: .one)
        dataSource.apply(snapshot, animatingDifferences: false)
    }
}

extension ViewController {
    @IBAction func shuffle(_ sender: Any) {
        Item.oneItem.shuffle()
        
        var snapshot = NSDiffableDataSourceSnapshot<Section, Item>()
        snapshot.appendSections([.one])
        snapshot.appendItems(Item.oneItem, toSection: .one)
        dataSource.apply(snapshot, animatingDifferences: true)
    }
}

```
  
## 参考  
  
- [UITableViewDiffableDataSource | Apple Developer Documentation](https://developer.apple.com/documentation/uikit/uitableviewdiffabledatasource)  
  