title:: Convert pb or onnx to IR
- 如何用mo将pb或者onnx文件转为IR
- ```
  export LD_LIBRARY_PATH=/lib:/usr/lib:/usr/local/lib:/mnt/sh_flex_storage/home/xuqian/anaconda3/envs/pot/lib/
  mo --input_model ./eisw_34068_graph.onnx --output_dir ./eisw
  ```