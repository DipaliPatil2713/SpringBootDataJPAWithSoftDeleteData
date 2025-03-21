# SpringBootDataJPAWithSoftDeleteData

# SpringBootJPAAndSoftDeleteData
Spring Boot Application with API and Soft Delete Data 

//Code for fetching data, Soft deleted data will not be fetched but saved in Database as it is.
//Service layer 
@Override
public Optional<Customer> findById(int custId) {
return Optional.ofNullable(customerRepository.findById(custId).map(customer -> customer.isDeleted() ? null : customer).orElse(null));}


//Data is only deleted from user end but saved in Database
// Controller Layer
@DeleteMapping("/deletebyid/{custId}")
    public ResponseEntity<String> deleteById(@PathVariable int custId) {

        Optional<Customer>customer =customerService.findById(custId);
        if (customer.isPresent()){
            Customer existingCustomer = customer.get();
            existingCustomer.setDeleted(true); // here we marked deleted
            customerService.save(existingCustomer);
            return ResponseEntity.ok("Customer Data Deleted Sucessfully");
        }else
        return ResponseEntity.notFound().build();
    }


//Repository Layer coding Custom Method
      // Fetch only non-deleted customers and for finding partial similar names and also by lowercase and uppercase data
    @Query("SELECT c FROM Customer c WHERE c.deleted = false AND LOWER(c.custName) LIKE LOWER(CONCAT('%', :custName, '%'))")
    List<Customer> findByCustName(String custName);