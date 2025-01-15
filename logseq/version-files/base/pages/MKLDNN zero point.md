- `dnnl.hpp`
- ```C++
      /// Appends an accumulation (sum) post-op. Prior to accumulating the
      /// result, the previous value will be will be reduced by zero point
      /// @p zero_point and multiplied by a scaling factor @p scale.
      ///
      /// The kind of this post-op is #dnnl::primitive::kind::sum.
      ///
      /// This feature may improve performance for cases like dequantize the
      /// asymmetrically quantized sum's src1 tensor to f32 domain before
      /// performing the sum operation by subtracting @p zero_point before the
      /// scaling.
      ///
      /// In the simplest case when the accumulation is the only post-op,
      /// the computations will be `dst[:] := scale * (dst[:] - zero_point) +
      /// op(...)` instead of `dst[:] := op(...)`.
      ///
      /// If @p data_type is specified, the original dst tensor will be
      /// reinterpreted as a tensor with the provided data type. Because it is a
      /// reinterpretation, data_type and dst data type should have the same size.
      /// As a result, computations will be `dst[:] <- scale *
      /// (as_data_type(dst[:]) - zero_point) + op(...)` instead of
      /// `dst[:] <- op(...)`.
      ///
      /// @note
      ///     This post-op executes in-place and does not change the
      ///     destination layout.
      ///
      /// @param scale Scaling factor.
      /// @param zero_point Zero point.
      /// @param data_type Data type.
      void append_sum(float scale = 1.f, int32_t zero_point = 0,
              memory::data_type data_type = memory::data_type::undef) {
          error::wrap_c_api(dnnl_post_ops_append_sum(get(), scale, zero_point,
                                    memory::convert_to_c(data_type)),
                  "could not append a sum post-op");
      }
  ```