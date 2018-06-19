## Crop 

```python
print("We load a polygon volume and use it to crop the original point cloud")
vol = read_selection_polygon_volume("../../TestData/Crop/cropped.json")
chair = vol.crop_point_cloud(pcd)
draw_geometries([chair])
print("")
```

```json
#./TestData/Crop/cropped.json File contents:

{
    "axis_max" : 4.022921085357666,
    "axis_min" : -0.76341366767883301,
    "bounding_polygon" : 
    [
        [ 2.6509309513852526, 0.0, 1.6834473132326844 ],
        [ 2.5786428246917148, 0.0, 1.6892074266735244 ],
        [ 2.4625790337552154, 0.0, 1.6665777078297999 ],
        [ 2.2228544982251655, 0.0, 1.6168160446813649 ],
        [ 2.166993206001413, 0.0, 1.6115495157201662 ],
        [ 2.1167895865303286, 0.0, 1.6257706054969348 ],
        [ 2.0634657721747383, 0.0, 1.623021658624539 ],
        [ 2.0568612343437236, 0.0, 1.5853892911207643 ],
        [ 2.1605399001237027, 0.0, 0.96228993255083017 ],
        [ 2.1956669387205228, 0.0, 0.95572746049785073 ],
        [ 2.2191318790575583, 0.0, 0.88734449982108754 ],
        [ 2.2484881847925919, 0.0, 0.87042807267013633 ],
        [ 2.6891234157295827, 0.0, 0.94140677988967603 ],
        [ 2.7328692490470647, 0.0, 0.98775740674840251 ],
        [ 2.7129337547575547, 0.0, 1.0398850034649203 ],
        [ 2.7592174072415405, 0.0, 1.0692940558509485 ],
        [ 2.7689216419453428, 0.0, 1.0953914441371593 ],
        [ 2.6851455625455669, 0.0, 1.6307334122162018 ],
        [ 2.6714776099981239, 0.0, 1.675524657088997 ],
        [ 2.6579576128816544, 0.0, 1.6819127849749496 ]
    ],
    "class_name" : "SelectionPolygonVolume",
    "orthogonal_axis" : "Y",
    "version_major" : 1,
    "version_minor" : 0
}

```


## RANSAC

> https://nbviewer.jupyter.org/url/lang.sist.chukyo-u.ac.jp/Classes/Open3D/Open3D.ipynb#RANSAC

```python
print("5. RANSAC registration on down-sampled point clouds.")
print("   Since the downsampling voxel size is 0.05, we use a liberal")
print("   distance threshold 0.075.")
result_ransac = registration_ransac_based_on_feature_matching(
        source_down, target_down, source_fpfh, target_fpfh,
        fpfh, max_correspondence_distance = 0.075,
        TransformationEstimationPointToPoint(False),
        ransac_n = 4,
        [CorrespondenceCheckerBasedOnEdgeLength(0.9),
        CorrespondenceCheckerBasedOnDistance(0.075)],
        RANSACConvergenceCriteria(max_iteration = 4000000, max_validation = 500))
print(result_ransac)
draw_registration_result(source_down, target_down,
        result_ransac.transformation)
```

