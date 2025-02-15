---
title: Julia数组索引
authors: Ethan Lin
year:
tags:
  - 内容/编程/Julia语言 
  - 知识 
  - 类型/举例 
---


# Julia数组索引






# Julia数组索引



Julia数组索引的例子：

## 例子：

```julia

julia> bi=Bool.([1 1 0;0 0 1;1 1 0])
3×3 BitMatrix:
 1 1 0
 0 0 1
 1 1 0
      
julia> br=Bool.([0,1,1])
3-element BitVector:
 0
 1
 1
      
     
julia> setbr=findall(br.==1)
2-element Vector{Int64}:
 2
 3
  
julia> setbi=findall(bi.==1)
4-element Vector{CartesianIndex{2}}:
 CartesianIndex(1, 1)
 CartesianIndex(3, 1)
 CartesianIndex(1, 2)
 CartesianIndex(3, 2)
 CartesianIndex(2, 3)

julia> (br .& bi)
3×3 BitMatrix:
 0 0 0
 0 0 1
 1 1 0     

julia> findall(br .& bi)
3-element Vector{CartesianIndex{2}}:
 CartesianIndex(3, 1)
 CartesianIndex(3, 2)
 CartesianIndex(2, 3)
 
julia> x=reshape(1:9,(3,3))*10
3×3 Matrix{Int64}:
 10 40 70
 20 50 80
 30 60 90
      
julia> v=sum(x,dims=1)'
3×1 adjoint(::Matrix{Int64}) with eltype Int64:
 60
 150
 240
       

julia> for i in (findall(br .& bi))
 println(x[i])
 end
30
600
80
      
julia> set1=[]
Any[]
  
julia> for i in 1:length(br)
 append!(set1,[Set(findall(bi[i,:]))])
 end
  
julia> set1
3-element Vector{Any}:
 Set([2, 1])
 Set([3])
 Set([2, 1])
 
```


## 例子：

```julia
julia> a
3×3 Matrix{Int64}:
 10  20  30
 40  50  60
 70  80  90

julia> pairs(IndexCartesian(), a)
pairs(::Matrix{Int64})(...):
  CartesianIndex(1, 1) => 10
  CartesianIndex(2, 1) => 40
  CartesianIndex(3, 1) => 70
  CartesianIndex(1, 2) => 20
  CartesianIndex(2, 2) => 50
  CartesianIndex(3, 2) => 80
  CartesianIndex(1, 3) => 30
  CartesianIndex(2, 3) => 60
  CartesianIndex(3, 3) => 90
```