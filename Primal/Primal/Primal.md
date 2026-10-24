---
notion-id: f4dc2651-2d70-4376-bdf8-098abef0d1ab
cover: "[[Primal.jpeg]]"
---
![[Untitled.png]]

> [!note]+ Mermaid Tamplate
> ```mermaid
> ---
> title: Overview of Spine Stenois Analysis
> ---
> graph LR    
>     subgraph aps-node
>     aps_a[apsCTSpineMeasure]
>     end
>     aps_a-->out_a["`DICOM
>                     -----------------
>                     **Auto Result**`"]
>                     
>     out_a-->app_a
> 
>     subgraph app-node
>     direction TB
>     app_a[Spinal Stenosis Page]
>     app_a-->app_b[User Override]-->app_c["`DICOM
>                                      -----------------
>                                     _Auto Result_
>                                     **＋User Result**`"]-->app_d[Report]    
>     app_a-->app_d[Report]
>     end
> 
>   
> 
> ```
> 
> ```mermaid
> ---
> title: Spine Stenosis Analysis Procedure
> ---
> graph TB
>      %% define nodes
>     start(("start"))
>     vertebra_seg("`**[AI]** Vertebra Segmentation`")
>     vertebra_label("`**[AI]** Vertebra Labeling`")
>     determine_disc{{Determine DiscLevel}}
>     invalid_disc(End)
>     make_spinal_curve(Make Spinal Curve)
>     reformat_mpr(Reformat MPR)
>     determine_metal{{Determine Metal}}
>     dural_sac_seg("`**[AI]** Dural-sac Segmentation`")
>     dural_sac_meas(Dural-sac Measurement)
>     finish(("end"))
> 
>     %% define links
>     start-->vertebra_seg --> vertebra_label-->determine_disc
>     determine_disc -->|Invalid| invalid_disc
>     determine_disc -->|Valid| make_spinal_curve
>     make_spinal_curve --> reformat_mpr
>     reformat_mpr --> dural_sac_seg
>     dural_sac_seg-->determine_metal -->|Not Exist| dural_sac_meas-->finish
> 
> ```

![[Primal 진행사항.base]]

![[D-log.base]]

### 참고자료

[[Christer_Ericson-Real-Time_Collision_Detection-EN.pdf]]

[The Care and Feeding of Modern Swap Chains (part 1)](https://walbourn.github.io/care-and-feeding-of-modern-swapchains/)

[Breaking Down Barriers - Part 1: What's a Barrier?](https://therealmjp.github.io/posts/breaking-down-barriers-part-1-whats-a-barrier/)

[Graphics Pipeline – Page 2 – The ryg blog](https://fgiesen.wordpress.com/category/graphics-pipeline/page/2/)

- Hardware관점에서의 Graphics pipeline에 대한 설명 
