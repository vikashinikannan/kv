// Initialize Books
const books = [
    { id: 1, title: "Book A", price: 300, stock: 10 },
    { id: 2, title: "Book B", price: 150, stock: 5 },
    { id: 3, title: "Book C", price: 700, stock: 8 },
  ];
  
  // Initialize Customers
  const customers = [
    { id: 101, name: "John Doe", isExistingCustomer: true, lastPurchase: null },
  ];
  
  // Function to calculate discount
  function calculateDiscount(price, isReturning) {
    if (isReturning) {
      return price > 200 ? price * 0.15 : 0;
    } else {
      if (price >= 100 && price <= 200) return price * 0.02;
      if (price > 200 && price <= 500) return price * 0.05;
      if (price > 500 && price <= 750) return price * 0.1;
      if (price > 750) return price * 0.15;
      return 0;
    }
  }
  
  // Main Function
  function processPurchase(bookIds, customerId) {
    // Find the customer
    const customer = customers.find(c => c.id === customerId);
    if (!customer) return "Customer not found.";
  
    // Count duplicates in bookIds
    const bookCounts = bookIds.reduce((counts, id) => {
      counts[id] = (counts[id] || 0) + 1;
      return counts;
    }, {});
  
    // Get books and calculate the bill
    let totalPrice = 0;
    let totalDiscount = 0;
  
    const booksPurchased = Object.entries(bookCounts).map(([id, quantity]) => {
      const book = books.find(b => b.id === parseInt(id));
      if (!book) return null; // Skip invalid book IDs
  
      const discount = calculateDiscount(book.price, customer.isExistingCustomer);
      const finalPrice = (book.price - discount) * quantity;
  
      totalPrice += finalPrice;
      totalDiscount += discount * quantity;
  
      // Update stock
      book.stock -= quantity;
  
      return {
        title: book.title,
        quantity,
        price: book.price,
        discount: discount * quantity,
        finalPrice,
      };
    }).filter(Boolean);
  
    // Update customer details
    customer.lastPurchase = {
      date: new Date().toISOString(),
      booksPurchased,
      totalAmount: totalPrice,
    };
  
    // Prepare the bill
    const bill = {
      customerName: customer.name,
      booksPurchased,
      totalPrice,
      totalDiscount,
    };
  
    return { bill, updatedStock: books, updatedCustomerDetails: customer };
  }
  
  // Test the function
  const bookIds = [1, 2, 2, 3, 1, 1]; // Customer selects some books multiple times
  const customerId = 101;
  
  console.log(processPurchase(bookIds, customerId));
  
