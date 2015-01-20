---
layout: post
title: "CollectionViewでのヘッダー固定"
date: 2014-02-21 12:57:47 +0900
comments: true
category: iOS
tags: [CollectionView,Custom]
---

コレクションビューでのヘッダーの固定（同じセクションの場合表示されっぱなしになるやつ）の方法がわからず検索していたら出会ったのでメモです。
  
<div class="github-widget" data-repo="griffin-stewie/CSNFloatingHeaderViewFlowLayout"></div>

<!-- more -->

## 実装
UICollectionViewFlowLayoutのサブクラスを作ることで実現すればいいみたいです。
[CSNFloatingHeaderViewFlowLayout](https://github.com/griffin-stewie/CSNFloatingHeaderViewFlowLayout)を見つけました・ω・

  
  
```
//
//  CSNFloatingHeaderViewFlowLayout.h
//
//  Created by griffin_stewie on 2013/10/14.
//  Copyright (c) 2013年 cyan-stivy.net. All rights reserved.
//

#import <UIKit/UIKit.h>

@interface CSNFloatingHeaderViewFlowLayout : UICollectionViewFlowLayout

/** zIndex for header
 default value is 1024
 */
@property (nonatomic, assign) NSInteger headerViewZIndex;

/** take over property values
 @return new instance
 */
- (instancetype)initWithFlowLayout:(UICollectionViewFlowLayout *)layout;

@end
```

```
//
//  CSNFloatingHeaderViewFlowLayout.m
//
//  Created by griffin_stewie on 2013/10/14. Based on https://gist.github.com/toblerpwn/5393460
//  Copyright (c) 2013年 cyan-stivy.net. All rights reserved.
//

#import "CSNFloatingHeaderViewFlowLayout.h"

@implementation CSNFloatingHeaderViewFlowLayout

- (void)commonInit;
{
    _headerViewZIndex = 1024;
}

- (id)init
{
    self = [super init];
    if (self) {
        [self commonInit];
    }
    
    return self;
}

- (id)initWithCoder:(NSCoder *)aDecoder
{
    self = [super initWithCoder:aDecoder];
    if (self) {
        [self commonInit];
    }
    
    return self;
}

- (instancetype)initWithFlowLayout:(UICollectionViewFlowLayout *)layout
{
    self = [self init];
    if (self) {
        self.minimumLineSpacing = layout.minimumLineSpacing;
        self.minimumInteritemSpacing = layout.minimumInteritemSpacing;
        self.itemSize = layout.itemSize;
        self.scrollDirection = layout.scrollDirection;
        self.headerReferenceSize = layout.headerReferenceSize;
        self.footerReferenceSize = layout.footerReferenceSize;
        self.sectionInset = layout.sectionInset;
    }
    
    return self;
}

- (NSArray *)layoutAttributesForElementsInRect:(CGRect)rect
{
    NSMutableArray *answer = [[super layoutAttributesForElementsInRect:rect] mutableCopy];
    UICollectionView * const collectionView = self.collectionView;
    CGPoint const contentOffset = collectionView.contentOffset;
    
    NSMutableIndexSet *missingSections = [NSMutableIndexSet indexSet];
    for (UICollectionViewLayoutAttributes *layoutAttributes in answer) {
        if (layoutAttributes.representedElementCategory == UICollectionElementCategoryCell) {
            [missingSections addIndex:layoutAttributes.indexPath.section];
        }
    }
    for (UICollectionViewLayoutAttributes *layoutAttributes in answer) {
        if ([layoutAttributes.representedElementKind isEqualToString:UICollectionElementKindSectionHeader]) {
            [missingSections removeIndex:layoutAttributes.indexPath.section];
        }
    }
    
    [missingSections enumerateIndexesUsingBlock:^(NSUInteger idx, BOOL *stop) {
        
        NSIndexPath *indexPath = [NSIndexPath indexPathForItem:0 inSection:idx];
        
        UICollectionViewLayoutAttributes *layoutAttributes = [self layoutAttributesForSupplementaryViewOfKind:UICollectionElementKindSectionHeader atIndexPath:indexPath];
        
        [answer addObject:layoutAttributes];
        
    }];
    
    for (UICollectionViewLayoutAttributes *layoutAttributes in answer) {
        
        if ([layoutAttributes.representedElementKind isEqualToString:UICollectionElementKindSectionHeader]) {
            
            NSInteger section = layoutAttributes.indexPath.section;
            NSInteger numberOfItemsInSection = [collectionView numberOfItemsInSection:section];
            
            NSIndexPath *firstObjectIndexPath = [NSIndexPath indexPathForItem:0 inSection:section];
            NSIndexPath *lastObjectIndexPath = [NSIndexPath indexPathForItem:MAX(0, (numberOfItemsInSection - 1)) inSection:section];
            
            BOOL cellsExist;
            UICollectionViewLayoutAttributes *firstObjectAttrs;
            UICollectionViewLayoutAttributes *lastObjectAttrs;
            
            if (numberOfItemsInSection > 0) { // use cell data if items exist
                cellsExist = YES;
                firstObjectAttrs = [self layoutAttributesForItemAtIndexPath:firstObjectIndexPath];
                lastObjectAttrs = [self layoutAttributesForItemAtIndexPath:lastObjectIndexPath];
            } else { // else use the header and footer
                cellsExist = NO;
                firstObjectAttrs = [self layoutAttributesForSupplementaryViewOfKind:UICollectionElementKindSectionHeader
                                                                        atIndexPath:firstObjectIndexPath];
                lastObjectAttrs = [self layoutAttributesForSupplementaryViewOfKind:UICollectionElementKindSectionFooter
                                                                       atIndexPath:lastObjectIndexPath];
                
            }
            
            CGFloat topHeaderHeight = (cellsExist) ? CGRectGetHeight(layoutAttributes.frame) : 0;
            CGFloat bottomHeaderHeight = CGRectGetHeight(layoutAttributes.frame);
            CGRect frameWithEdgeInsets = UIEdgeInsetsInsetRect(layoutAttributes.frame,
                                                               collectionView.contentInset);
            
            CGPoint origin = frameWithEdgeInsets.origin;
            UIEdgeInsets sectionInset = self.sectionInset;
            
            origin.y = MIN(
                           MAX(
                               contentOffset.y + collectionView.contentInset.top,
                               (CGRectGetMinY(firstObjectAttrs.frame) - topHeaderHeight - sectionInset.top)
                               ),
                           (CGRectGetMaxY(lastObjectAttrs.frame) - bottomHeaderHeight + sectionInset.bottom)
                           );

            
            layoutAttributes.zIndex = self.headerViewZIndex;
            layoutAttributes.frame = (CGRect){
                .origin = origin,
                .size = layoutAttributes.frame.size
            };
            
        }
        
    }
    
    return answer;
}

- (BOOL) shouldInvalidateLayoutForBoundsChange:(CGRect)newBound
{
    return YES;
}
@end
```

## ライセンス情報

```
The MIT License (MIT)

Copyright (c) 2013 griffin-stewie

Permission is hereby granted, free of charge, to any person obtaining a copy of
this software and associated documentation files (the "Software"), to deal in
the Software without restriction, including without limitation the rights to
use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of
the Software, and to permit persons to whom the Software is furnished to do so,
subject to the following conditions:

The above copyright notice and this permission notice shall be included in all
copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS
FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR
COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER
IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN
CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
```

## リンク
- [UICollectionView で UITableView のセクションヘッダー風の SupplementaryView を実装する](http://qiita.com/griffin_stewie/items/7e8b9ef40481af18286f)
- [CSNFloatingHeaderViewFlowLayout](https://github.com/griffin-stewie/CSNFloatingHeaderViewFlowLayout/tree/master/CSNFloatingHeaderViewFlowLayout)