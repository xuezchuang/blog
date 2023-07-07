title: blender_1
author: xuezc
abbrlink: a36a2e33
tags:
  - graph
  - blender
categories:
  - blender
description: 以Edit Mode状态下选取顶点为例分析
date: 2023-06-29 13:08:00
---
blender 源码分析 Mesh
# struct
```
结构体MeshBatchList中包含了GPUBatch.draw使用.
结构体MeshBufferList中包含GPUVertBuf和GPUIndexBuf,也就是vbobuffer的数据.
struct MeshBufferCache
{
  MeshBufferList buff;
  MeshExtractLooseGeom loose_geom;
  SortedPolyData poly_sorted;
};
```
```c
struct MeshBatchCache 
{
  MeshBufferCache final, cage, uv_cage;
  MeshBatchList batch;
}
```
# 流程
对cache的填充是在wm_event_do_handlers的流程下的.
```
列举出以为重要的函数堆栈
1.wm_event_do_handlers
2.wm_handler_operator_call
3.WM_gesture_box_modal
4.gesture_box_apply
5.view3d_box_select_exec
6.DRW_draw_select_id
7.drw_engines_cache_populate
8.DRW_mesh_batch_cache_create_requested

```

# DRW_mesh_batch_cache_create_requested
可以通过Mesh获取到缓冲结构体MeshBatchCache
```
Mesh *me,
MeshBatchCache *cache = mesh_batch_cache_get(me);
```
MeshBatchCache将final的buff中的vbo给到batch中.
之后在mesh_buffer_cache_create_requested函数中分配任务,在extract_task_range_run任务中修改vbo数据.
```
MeshBufferList *mbuflist = &cache->final.buff;
DRW_vbo_request(cache->batch.edit_triangles, &mbuflist->vbo.edit_data);
mesh_buffer_cache_create_requested
BLI_task_graph_work_and_wait
```