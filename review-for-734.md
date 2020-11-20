# Review for 734

## Overall quality assessment

B+ / Accept

## Comments to the author

This paper proposes a novel learning-based method to tackle the practical task of SLAM-oriented point cloud compression, which aims at select a compact subset of points to reserve localization precision. Specifically, observation counts are calculate via matching between the map and LiDAR scans to evaluate priority levels of each point in one frame. Furthermore, this method takes this task as segmentation to predict the priority of points, and then applies different strategies for them to accomplish compression. Experiments are conducted by intergrating the network into an existing SLAM system, which shows that the network achieves the higher compression ratio of maps but maintaines much localization accuracy with respect to other approaches. Besides, evaluations on the Ford Campus dataset with the model only trained on KITTI shows the generalization ability of the proposed method.

This paper is well-structured, and the results with compelling visualizations are presented clearly. Besides, the authors explain the intuition behind each step of their proposed method very well. The novelty lies in leveraging the relationship between the priority for SLAM and the geometric features of one frame point clouds, which proves that the feasibility for compression without pre-building global maps. This paper provides a promising research direction for adequately exploring point clouds in SLAM applications. Sufficient experiments demonstrate the effectiveness and generalization of the method and the impressive speedup and storage saving. Moreover, this compression method can be integrated into current SLAM systems , which shows its prospects for real-world applications. Still, I would like to give some suggestions for further considerations: 

    1. The sequence of LiDAR frames, to some extent, has an impact on the localization. It seems that sequential informations can be utilized further to achieve better performance. 

    2. More ablation studies of parameter settings \(e.g., the threshold distances for observation or not\) can be performed for a stronger explanation.

Overall, I am very positive about the paper and hence recommend the acceptance of this paper.

**Minor Suggestions:**

* In Sec III.C, the formulation of the attention result has a vague description of dimensions D1 and D2.
* Fig. 4 lacks the legend for points labeled as low priority and ground.

