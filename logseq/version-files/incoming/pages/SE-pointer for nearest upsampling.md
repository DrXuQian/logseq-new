- ```c++
  // Sub function to generate storage element pointer for InterpNN
  
  void populateActivationStorageElementMapForInterpNN(mv::Data::OpListIterator dpuTaskOp, mv::ComputationModel&)
  {
      auto input = dpuTaskOp->getInputTensor(0);
      auto activationStorageElement = dpuTaskOp->getInputTensor(dpuTaskOp->
                                              get<std::vector<std::size_t>>("storageElementIndex")[0]);
  
      auto width = activationStorageElement->getShape()[mv::IO_WIDTH_DIMENSION];
      auto height = activationStorageElement->getShape()[mv::IO_HEIGHT_DIMENSION];
      std::vector<int64_t> unpopulated_offsets(width*height, 0);
  
      auto inputChannels = input->getShape()[mv::IO_CHANNEL_DIMENSION];
      long int channelIncrement = inputChannels * (input->getDType().getSizeInBits() / 8) ;
  
      auto originalShape = activationStorageElement->get<mv::Shape>("originalShape");
      auto old_width = originalShape[mv::IO_WIDTH_DIMENSION];
      auto old_height = originalShape[mv::IO_HEIGHT_DIMENSION];
      auto new_width = activationStorageElement->getShape()[mv::IO_WIDTH_DIMENSION];
      auto new_height = activationStorageElement->getShape()[mv::IO_HEIGHT_DIMENSION];
      auto scale_factor_width = int(new_width/old_width);
      auto scale_factor_height = int(new_height/old_height);
  
      // Populate SEP table
      for (auto row=0; row < int(new_height / scale_factor_height); ++row){
          for (auto col=0; col < int(new_width / scale_factor_width); ++col){
              auto old_offset = (row*old_width + col) * channelIncrement;
              for (auto new_row=0; new_row < scale_factor_height; ++new_row){
                  for (auto new_col=0; new_col < scale_factor_width; ++new_col){
                      auto new_offset = row*new_width*scale_factor_height + new_row*new_width + col*scale_factor_width + new_col;
                      unpopulated_offsets[new_offset] = (old_offset << SHIFT_FOR_STORAGE_ELEMENT);
                  }
              }
          }
      }
      activationStorageElement->populate(unpopulated_offsets, mv::Order("NHWC"));
  }
  ```