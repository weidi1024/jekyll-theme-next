---
title: Faster RCNN_1_ 一些阈值的设置
date: 2018-04-11 22:00:00
categories: Faster RCNN学习笔记
tags: Faster_RCNN
mathjax: true

---

***

*Faster RCNN_1_ 一些阈值的设置*

***
***


# 一、faster rcnn的一种训练方式
联合训练

- 1 stage1_rpn_train
- 2 stage1_fast_rcnn_train
- 3 stage2_rpn_train
- 4 stage2_fast_rcnn_train


# 二、一些阈值的设置
## 1 stage1_rpn_train.pt

    layer {
      name: 'rpn-data'
      type: 'Python'
      bottom: 'rpn_cls_score'
      bottom: 'gt_boxes'
      bottom: 'im_info'
      bottom: 'data'
      top: 'rpn_labels'
      top: 'rpn_bbox_targets'
      top: 'rpn_bbox_inside_weights'
      top: 'rpn_bbox_outside_weights'
      python_param {
         module: 'rpn.anchor_target_layer'
         layer: 'AnchorTargetLayer'
         param_str: "'feat_stride': 16"
       }
    }
    

原理：对产生的anchor进行标记，确定是前景还是背景。通过anchor与ground truth box的overlap来决定是否具有前景。

python层：rpn.anchor_target_layer

    labels[max_overlaps >= cfg.TRAIN.RPN_POSITIVE_OVERLAP] = 1
    labels[max_overlaps < cfg.TRAIN.RPN_NEGATIVE_OVERLAP] = 0

阈值设置：fast_rcnn.config.py

    __C.TRAIN.RPN_POSITIVE_OVERLAP = 0.7
    __C.TRAIN.RPN_NEGATIVE_OVERLAP = 0.3

将IOU大于0.7的设置为前景，将IOU小于0.3的设置为背景。


## 2 rpn_test.py

    layer {
      name: 'proposal'
      type: 'Python'
      bottom: 'rpn_cls_prob_reshape'
      bottom: 'rpn_bbox_pred'
      bottom: 'im_info'
      top: 'rois'
      top: 'scores'
      python_param {
         module: 'rpn.proposal_layer'
         layer: 'ProposalLayer'
         param_str: "'feat_stride': 8"
      }
    }

python层：rpn.proposal_layer

阈值设置：fast_rcnn.config.py

    # NMS threshold used on RPN proposals
    __C.TRAIN.RPN_NMS_THRESH = 0.7

产生proposals之后的NMS抑制的阈值。

    # Number of top scoring boxes to keep before apply NMS to RPN proposals
    __C.TRAIN.RPN_PRE_NMS_TOP_N = 12000
    # Number of top scoring boxes to keep after applying NMS to RPN proposals
    __C.TRAIN.RPN_POST_NMS_TOP_N = 2000


## 3 stage1_fast_rcnn_train.pt
    layer {
      name: 'data'
      type: 'Python'
      top: 'data'
      top: 'rois'
      top: 'labels'
      top: 'bbox_targets'
      top: 'bbox_inside_weights'
      top: 'bbox_outside_weights'
      python_param {
         module: 'roi_data_layer.layer'
         layer: 'RoIDataLayer'
         param_str: "'num_classes': 21"
      }
    }

原理：对RPN提供的proposals进行处理。选择前景ROI和背景ROI

python层：roi_data_layer.layer

    oi_data_layer.minibatch.py
    fg_inds = np.where(overlaps >= cfg.TRAIN.FG_THRESH)[0]
    bg_inds = np.where((overlaps < cfg.TRAIN.BG_THRESH_HI) &
       (overlaps >= cfg.TRAIN.BG_THRESH_LO))[0]

阈值设置：fast_rcnn.config.py

    __C.TRAIN.FG_THRESH = 0.5
    __C.TRAIN.BG_THRESH_HI = 0.5
    __C.TRAIN.BG_THRESH_LO = 0.1

将IOU大于等于0.5的设置为前景，将IOU大于等于0.1，小于0.5之间的设置为背景。






## 4 其他

阈值设置：fast_rcnn.config.py

    # Overlap threshold for a ROI to be considered foreground (if >= FG_THRESH)
    __C.TRAIN.FG_THRESH = 0.5

大于0.5的ROI才会被使用bbox 回归	


    ## NMS threshold used on RPN proposals
    __C.TEST.RPN_NMS_THRESH = 0.7
    ## Number of top scoring boxes to keep before apply NMS to RPN proposals
    __C.TEST.RPN_PRE_NMS_TOP_N = 6000
    ## Number of top scoring boxes to keep after applying NMS to RPN proposals
    __C.TEST.RPN_POST_NMS_TOP_N = 300

测试阶段，产生proposals的NMS阈值。

    # Overlap threshold used for non-maximum suppression (suppress boxes with
    # IoU >= this threshold)
    __C.TEST.NMS = 0.3

检测完之后对bbox进行NMS抑制的阈值


